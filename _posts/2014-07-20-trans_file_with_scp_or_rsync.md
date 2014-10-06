---
layout: post
title: "两台机器间传输文件"
description: ""
category: "开发运营"
tags: [scp, rsync]
---
{% include JB/setup %}

两台linux机器间传输文件夹，对scp和rsync进行了简单的对比。

条件：

文件夹大小：3.1G，包含若干个小文件

两台内网linux机器


scp普通传输

	time scp -rp tgame_bak/ tgame@10.12.190.90://data/tgame/test
	real   0m56.284s
	user   0m0.300s
	sys 0m1.992s

scp压缩传输

	time scp -rp -C tgame_bak/ tgame@10.12.190.90://data/tgame/test
	real   3m3.504s
	user   2m40.146s
	sys 0m5.960s

ssh压缩传输

	time tar cvzf - tgame_bak | ssh tgame@10.12.190.90 "cd /data/tgame/test; tar xvzf -"
	real   2m47.822s
	user   2m41.662s
	sys 0m3.120s

rsync传输

	ttime rsync -ave ssh tgame_bak tgame@10.12.190.90:/data/tgame/test/
	real    0m38.093s
	user    0m19.873s
	sys     0m5.252s


rsync压缩

	time rsync -aze ssh tgame_bak tgame@10.12.190.90:/data/tgame/test/
	real    2m38.995s
	user    2m40.698s
	sys     0m4.496s

可以看出，在这次传输过程中，使用rsync要比scp快很多，而且不压缩比压缩要快，单独压缩这个文件夹要2m30s左右。

如果单独传输压缩好后的压缩包，rsync和scp都用12.7左右，不相上下。

应该是rsync对于多文件传输有特殊的处理，才导致性能相差这么多。而且scp在传多个文件的时候速度要比传输一个相同大小的文件要满，所以才有了要压缩后再传的想法。

压缩后传输的时间分为两部分，压缩时间+传输压缩后包的时间；如果不压缩时间为传输压缩前大小的时间；相差的时间为压缩的时间和压缩后减少大小传输的时间之差，如果大于0，则不压缩为好，如果小于0，则压缩后传输好。

**结论：**

- 选择rsync要不scp传输要好；
- 如果估算下压缩的时间，和压缩后节省的空间传输用的时间对比，如果压缩时间长就不压缩。


*至于rsync和scp的性能为什么差这么多，还要研究。*
