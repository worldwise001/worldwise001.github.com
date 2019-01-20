---
author: worldwise001
comments: true
date: 2018-03-25 08:28:09+00:00
layout: post
link: http://blog.shh.sh/2018/03/25/indoor-growing-project-part-5/
slug: indoor-growing-project-part-5
title: Indoor Growing Project – Part 5
wordpress_id: 865
categories:
- Computers
- Green Things
---

This post is part of a multi-part series on my indoor growing. If you want, you can start from [Part 1](https://blog.shh.sh/2018/02/11/indoor-growing-project-part-1/).

It's been a while since I last updated, since life and such has gotten in the way. If you recall one or two posts ago, I acquired some sensors to try and wire up to monitor the greenhouse "health" (where the greenhouse = my front porch area) so that I can have metrics and graphs for such.

I started writing code for the sensors about a week ago. The idea I had was to use a small embedded to track sensor data and periodically either save it to a database, or publish it to some external metrics tracking system. Normally people would use an Arduino or similar, but I wanted the additional complexity of external API calls, having maybe a local webserver/page that shows data, and also the database storage, so that meant I needed something a bit bigger, and what was readily available to me was a Raspberry Pi 3.

The sensors I got all use the I2C bus to communicate, which luckily is provided with the pi-specific driver i2c_bcm2835. However since I'm writing my own userspace code, I wanted the i2c interface a bit more readily available to develop on. Fortunately this seems possible through the i2c_dev driver, which exposes the bus in /dev/i2c-?.  For some reason my sensors show up in /dev/i2c-1, out of the 3 possible interfaces (3 possible busses?):

![Screen Shot 2018-03-25 at 1.11.29 AM.png](https://worldwise001.files.wordpress.com/2018/03/screen-shot-2018-03-25-at-1-11-29-am.png)

You can rudimentarily poke at these interfaces using the i2c-tools package:

![Screen Shot 2018-03-25 at 1.12.23 AM.png](https://worldwise001.files.wordpress.com/2018/03/screen-shot-2018-03-25-at-1-12-23-am.png)

Specifically you can use i2cdetect to detect devices on the bus:

![Screen Shot 2018-03-25 at 1.11.16 AM.png](https://worldwise001.files.wordpress.com/2018/03/screen-shot-2018-03-25-at-1-11-16-am.png)

I have my BME280/CCS811 combo sensor hooked up, which shows up as two I2C devices; the BME280 sensor is at address 0x77, while the CCS811 sensor is at address 0x5b. So now I needed to figure out how to write code to talk to I2C bus to extract data out of these sensors.

Common sense (i.e. my previous interactions with writing code for TPMs) told me that I probably just needed to open the file interface and shuffle through some bytes, but the question was how. The arduino libraries provided by sparkfun were not very helpful, likely because there is a different method of interaction with the arduino i2c bus than in linux.

I googled "i2c linux" and came across this page which gave some helpful information: [https://elinux.org/Interfacing_with_I2C_Devices](https://elinux.org/Interfacing_with_I2C_Devices) . It appears the magical formula for interacting with I2C devices in Linux is as follows:



	
  1. Open the file, acquire file descriptor

	
  2. ioctl the file descriptor with I2C_SLAVE with the address of the target device

	
  3. read/write as normal (probably device-specific)


Now came to the writing the code part. I could write this easily in C, since that is what I am most familiar with, but the overhead of having to deal with C libraries for things like writing to databases or interacting with webservers promised a bunch of pain. I also really disliked writing Makefiles. So I took this opportunity to learn a new language that I had heard about recently, called Rust. Overall my first impressions are that while the syntax and memory management system takes some getting used to, it's actually fairly easy and fast to get started, and the compiler is pretty helpful at pointing you at the right direction when you're stuck. If you're an established programmer, you should probably be able to pick up the language within a few days.

I haven't gotten terribly far yet, but I do have some working code to at least extract a temperature for now:

![Screen Shot 2018-03-25 at 1.26.36 AM.png](https://worldwise001.files.wordpress.com/2018/03/screen-shot-2018-03-25-at-1-26-36-am.png)

That's all for now. If you'd like to follow along on my coding expeditions in rust for the rest of the sensors, all of it is on my github: [https://github.com/worldwise001/greenhouse](https://github.com/worldwise001/greenhouse) .

Continue on to [Part 6](http://blog.shh.sh/2018/04/01/indoor-growing-project-part-6/).
