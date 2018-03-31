title: 解决 release-stripped.ap_' specified for property 'resourceFile' does not exist.
author: superman
tags: []
categories:
  - gradle
date: 2018-02-08 09:53:00
---
设置buildTypes里的release的shrinkResources为false即可,如果是 release-stripped.ap_' specified for property 'resourceFile' does not exist.则设置buildTypes里的debug的shrinkResources为false