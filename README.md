Frigate NVR with Docker
This repository contains a docker-compose.yml file to run Frigate, an open-source Network Video Recorder (NVR) with real-time AI object detection.

What is Frigate?
Frigate is a powerful, open-source NVR built for security camera enthusiasts who want to leverage AI for object detection. It uses local processing to analyze video feeds, identify objects (like people, cars, and animals), and trigger events or recordings.

Key features of Frigate include:

AI-Powered Detection: Integrates with Google Coral TPUs, NVIDIA GPUs (via TensorRT), and OpenVINO to provide fast and accurate object detection on your own hardware.

Low Overhead: It's designed to be efficient, using a low-resolution sub-stream from your camera for detection and only saving high-resolution footage when an object is detected.

Web Interface: A clean UI for viewing camera streams, events, and recordings.

Home Assistant Integration: Excellent integration with Home Assistant for creating advanced automations.

Privacy Focused: Since everything runs locally, your video footage stays on your network.

About This docker-compose.yml File
This configuration is specifically tailored to run Frigate on a machine with an NVIDIA GPU, leveraging the TensorRT engine for hardware-accelerated object detection.

Configuration Breakdown
image: ghcr.io/blakeblackshear/frigate:stable-tensorrt-jp6

This pulls the official Frigate image that is optimized for NVIDIA Jetson Platform (JP6) devices and includes support for TensorRT, NVIDIA's high-performance deep learning inference engine.

runtime: nvidia & deploy: resources: ...

These are crucial directives that grant the Docker container access to the host machine's NVIDIA GPU. This enables Frigate to offload the heavy AI processing work to the GPU, significantly improving performance and freeing up the CPU.

shm_size: '1g'

Frigate uses shared memory (/dev/shm) extensively for video processing. This line allocates 1 GB of shared memory to the container, which is a common requirement to prevent crashes when processing high-resolution video streams.

volumes:

- /stuff/docker/frigate/config:/config: Configuration Directory. This maps a local directory to the container's /config directory. You must place your config.yml file here.

- /stuff/docker/frigate/storage:/media/frigate: Storage Directory. This is where Frigate will save all its video recordings and snapshots.

type: tmpfs ... target: /tmp/cache: RAM Cache. This mounts a temporary filesystem in RAM for caching. This improves performance for segmented video clips and reduces wear and tear on your physical storage drive (like an SSD). It is configured here with a 1GB limit.

ports:

5000:5000: Exposes the Frigate web UI.

8554:8554: Exposes the RTSP port, allowing you to view camera streams processed by Frigate in other applications like VLC.

environment:

YOLO_MODELS=yolov7-320: Specifies the YOLO (You Only Look Once) object detection model to be used.

USE_FP16=false: A setting for model precision.

PLUS_API_KEY: Your API key for Frigate+, an optional service for training custom models. Note: Do not commit your real key to public repositories.

How to Use
Prerequisites
Docker & Docker Compose: Ensure you have both installed on your system.

NVIDIA GPU: You need a compatible NVIDIA graphics card.

NVIDIA Drivers: The appropriate NVIDIA drivers for your OS must be installed.

NVIDIA Container Toolkit: This is required to allow Docker containers to access the GPU.

Steps
Clone or Download: Get the docker-compose.yml file onto your machine.

Create Directories: On your host machine, create the directories for the configuration and storage volumes:

Bash

mkdir -p /stuff/docker/frigate/config
mkdir -p /stuff/docker/frigate/storage
Create Configuration File: Create a config.yml file inside the /stuff/docker/frigate/config directory. You will need to define your MQTT broker, cameras, and detectors in this file. Refer to the official Frigate documentation for guidance.

Update API Key (Optional): If you use Frigate+, replace the placeholder in the docker-compose.yml file with your actual API key.

YAML

environment:
  - PLUS_API_KEY=<YOUR_FRIGATE_PLUS_API_KEY>
Start the Container: Run the following command from the same directory as your docker-compose.yml file:

Bash

docker-compose up -d
Access Frigate: Open a web browser and navigate to http://<YOUR_SERVER_IP>:5000. You should see the Frigate UI.



