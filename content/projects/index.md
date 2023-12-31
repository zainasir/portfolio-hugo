---
title: "Projects"
draft: false
hidemeta: true
comments: false
description: "These are some of the projects I have done in my spare time. If you find anything interesting and want to extend it further, feel free to contact me."
disableShare: true
---

## [Ball-following Robot](https://github.com/zainasir/BallFollower)
ROS project where a TurtleBot follows a red ball while maintaining a distance of 1 meter. I used OpenCV to detect a sphere and the most red pixel in the camera's viewport to pinpoint the ball's exact location. After finding the distance of the ball using the depth camera, linear and angular velocity commands were sent to the TurtleBot, allowing it to follow the red ball while maintaining the distance.

![Gif showing a demo of this project](assets/ball-follower-robot-demo.gif)

## [Spotted Lantern Fly Detection through Drones](https://github.com/boubinjg/SpottedLanternFly)
The aim of this project was to enable drones to be able to detect Spotted Lantern Flys through object detection. This project was completed as part of the Digital Ag Hackathon at the Cornell Institute for Digital Agriculture. We collected a custom dataset, retrained YOLOv8, and integrated trained model in a drone for aerial surveillance.

![Gif showing a demo of this project](assets/slf-demo.gif)

## [Map Navigation through Deep Q-learning](https://github.com/zainasir/FrozenLakeDQN)
A research project with the aim of exploring deep q-learning as a tool to navigate dynamic maps. I utilized FrozenLake environment as a research platform, developed a data pipeline to extract states as images, and generated optimal policy using a two-part neural network.

![Gif showing a demo of this project](assets/map-navigation.gif)

## [Pathfinding Visualizer](https://github.com/zainasir/PathfindingVisualizer)
A Java program developed to visualize A* pathfinding algorithm in real-time. I implemented the A* algorithm in Java to find the shortest distance between two points on a square grid and utilized Swing toolkit to show the algorithm searching possible paths in real-time.

![Gif showing a demo of this project](assets/pathfinding.gif)
