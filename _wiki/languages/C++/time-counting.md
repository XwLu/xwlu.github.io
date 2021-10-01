---
layout: wiki
title: Time Counting 
categories: [C++]
description: Time counting for C++
keywords: time-counting, C++
---

  ```
  #include <chrono>
  auto time1 = std::chrono::system_clock::now();
  auto time2 = std::chrono::system_clock::now();
  std::chrono::duration<double> diff = time2 - time1;
  ADEBUG << "Time Diff = " << diff.count() * 1000<< " msec.";
  ```