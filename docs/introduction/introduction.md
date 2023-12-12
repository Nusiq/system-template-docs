(system-template-filter)=
# System Template Introduction

System Template is a filter that aims to improve the reusability of the code in Minecraft Resource Packs and Behavior Packs, by grouping the closely related files into systems. It greatly improves the ease of copying the code between the projects, even if the systems modify the same files thanks to its merging capabilities. The files in a system can use variables configured in the global and system's scope. A higly configurable systems can be used in the same project multiple times, with different configurations, hence the name "System Template" - a configurable system can be used as a template for creating multiple things.

A system can have various forms. It's up to you to decide how to group your code. Below there are some examples of things that can be implemented as systems:
- a single Minecraft entity
- multiple Minecraft entities that cannot exist without each other
- an armor set with all of its game logic

System templates and easy to reuse between multiple project, because every template has its dedicated folder. In most cases importing a system to another project is as easy as copying the folder with the system.
