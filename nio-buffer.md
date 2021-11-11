### NIO Buffer
NIO中会用到用于读写的buffer，包括netty已使用到相关的buffer，以下对该java.nio.Buffer进行描述；

Buffer类似于pipe，是用于进程或者线程通信用的。主要的属性有capacity,limit,position，其中
 	 capacity是容量，表示buffer的最大容量；
	 limit是第一个不可读或者不可写的元素位置，很明显limit一定比capacity小；
	 position是下一个读或者写的元素位置；

其他概念：
	1. Mark 和 reset。 通过设置mark属性，当mark不为-1时，每一次reset都会reset到mark属性的值，mark相当于游戏里面的存档；当然mark值不能超过当前position；（存档，恢复）
	
	2. clear: 清空buffer，讲position设置为0，limit设置为capacity;（清空buffer）
	
	3. flip： 读取buffer里面的数据，将position设置为0，limit设置为之前的position；(读取buffer中已经写入的数据buffer)
	
	4. rewind: 将当前的position设置为0，mark设置为-1，可以重新读取buffer中的数据。（重新读取buffer数据）
