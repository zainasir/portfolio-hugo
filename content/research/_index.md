---
title: "Research"
showToc: true
TocOpen: false
draft: false
hidemeta: true
comments: false
description: "My current research involves autonomous navigation systems for UAVs and drones, utilizing ROS, OpenCV, and CUDA. In the past, I have also explored intelligent task planning through large language models."
disableHLJS: false # to disable highlightjs
disableShare: true
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowPostNavLinks: false
ShowWordCount: true
ShowRssButtonInSectionTermList: false
UseHugoToc: true
---
## Autonomous Navigation and Path Planning
I am developing network-aware edge-based SLAM techniques and tailoring them for deployment on small drones with limited computation power. I have developed a robust data pipeline that transmits live video feed from drones to a SLAM system for feature detection and map construction. My system is also compatible with Docker for efficient deployment and portability.

I have also incorporated network analysis in the Edge-SLAM. By monitoring latency and throughput of the live video feed in real-time, the SLAM system can dynamically switch between edge processing and client processing. This enables [Edge-SLAM](https://github.com/zainasir/edgeslam/tree/docker) to continue mapping even under suboptimal connectivity conditions, ensuring a resilient and adaptable solution for autonomous drone navigation in extreme outdoor environments.

### Results

The testing setup included Aurelia X6 Standard drone, ZED Depth camera for live depth data, and Jetson AGX Xavier for SLAM computation. The ZED camera and Jetson were attached under the drone.
{{< figure src="assets/drones.png" attr="Aurelia X6 Standard with ZED Depth Camera and Jetson AGX Xavier were used for testing purposes." align=center >}}

Testing was carried out in the Engineering Building courtyard at Binghamton University. Videos below show features and the drone location detected by our SLAM algorithm in real-time.

{{< figure src="assets/demo_3.gif" attr="Dense point cloud generated in a feature-rich environment by our SLAM algorithm." align=center >}}
{{< figure src="assets/demo_1.gif" attr="Our SLAM algorithm is able to detect the floor and the trees around the drone, even when the number of obstacles in front is sparse." align=center >}}
{{< figure src="assets/demo_2.gif" attr="Feature detection between walls and trees." align=center >}}

## Robot Task Planning with LLMs
During my undergraduate research, I explored the capabilities of large language models like GPT-3 in interpreting grounded instructions for everyday tasks which can be executed by robots. Through prompt engineering and parameter optimization, I was able to demonstrate that LLMs can efficiently understand and break up everyday tasks into executable actions without retraining or fine-tuning.

To read more about the implementation, experiments, and results, please check out the paper [here](/analyzing-capabilities-gpt-intelligent-task-execution.pdf).
