# Running QEMU with persistent disks(虚拟磁盘运行QEMU)


It's useful to run QEMU with persistent disks, like you'd have on actual
hardware.

虚拟硬盘运行`QEMU`是非常有用的，就像使用实际硬盘一样。


Specifically you'd want a `/data` minfs partition and a `/blobstore` blobfs
partition, but `/system` and `/boot` from your local build. Here's how to do
that.

具体来说，你需要一个`/data` minfs 分区和`/blobstore` blobfs分区，但，  需要在本地创建`/system` and `/boot`。

*NOTE:* Lines that begin with a `$` should be typed. Other lines are example
output.

请注意，输入的行以`$`开头。其他行是示例输出。


## Create the disk image(创建硬盘映像)
`blk.bin` is the name of the image that `frun` / `mrun` looks for when you pass
`-d`. Make a 1g one in your `$FUCHSIA_DIR`.

`blk.bin`是`frun` /`mrun`在通过`-d`时查找的映象名称。在`$FUCHSIA_DIR`中，分配给它1G存储。

Linux:
```
$ cd $FUCHSIA_DIR
$ truncate -s 1g blk.bin
```
macOS:
```
$ cd $FUCHSIA_DIR
$ mkfile -n 1g blk.bin
```


## Start zircon(启动Zircon)
You don't need a full Fuchsia UI to set up the disk image so start just zircon
but tell it to mount

你不需要一个完整的Fuchsia UI来设置磁盘映像，所以只需启动zircon并告诉它挂载
 ```
$ mrun -d
[00000.000] 00000.00000> multiboot: info @ 0xffffff8000009500
[00000.000] 00000.00000> multiboot: cmdline @ 0xffffff8000253059
[00000.000] 00000.00000> multiboot: ramdisk @ 00254000..0ec607e0
[00000.000] 00000.00000> bootdata: @ 0xffffff8000254000 (245417952 bytes)
[00000.000] 00000.00000>
[00000.000] 00000.00000> welcome to lk/MP
(etc)
```
## Initialize the GPT(初始化GPT)

You blank `blk.bin` image needs a partition table.

空白`blk.bin`需要添加至分区列表。

```
$ gpt init /dev/class/block/000
blocksize=0x200 blocks=2097152

WARNING: You are about to permanently alter 

警告：您即将永久更改 /dev/class/block/000

Type 'y' to continue, any other key to cancel

输入y继续，其他键取消

invalid header magic!
[00031.068] 02004.02044> device: 0x4e3af554b000(sata0): ref=0, busy, not
releasing
[00031.070] 01043.01046> devcoord: drv='block' bindable to dev='sata0'
[00031.072] 01043.01166> devmgr: new block device: /dev/class/block/001
GPT changes complete.
[00031.077] 01043.01166> devmgr: /dev/class/block/001: GPT?
[00031.078] 01043.01046> devcoord: dc_bind_device() '/boot/driver/gpt.so'
[00031.078] 01043.01046> devcoord: drv='gpt' bindable to dev='block'
```


## Create the partitions(创建分区)


Now that there's a blank partition table, create a 500m data and 500m blob
partition: 
现在，在一个空白的分区列表，创建一个500M数据和500M blob分区

```
$ gpt repartition /dev/class/block/001 data data 500m blob blobfs 500m
blocksize=0x200 blocks=2097152
data: 524288000 bytes, 1024000 blocks, 48-1024063
blob: 524288000 bytes, 1024000 blocks, 1024064-2048079
[00242.582] 02004.02044> device: 0x4e3af554b000(sata0): ref=0, busy, not
releasing
[00242.584] 01043.01046> devcoord: drv='block' bindable to dev='sata0'
[00242.587] 01043.01166> devmgr: new block device: /dev/class/block/002 GPT
changes complete.
[00242.594] 01043.01166> devmgr: /dev/class/block/002: GPT?
[00242.596] 01043.01046> devcoord: dc_bind_device() '/boot/driver/gpt.so'
[00242.596] 01043.01046> devcoord: drv='gpt' bindable to dev='block'
[00242.619] 01043.01046> devcoord: drv='block' bindable to dev='part-000'
[00242.622] 01043.01166> devmgr: new block device: /dev/class/block/003
[00242.624] 01043.01046> devcoord: drv='block' bindable to dev='part-001'
[00242.628] 01043.01166> devmgr: new block device: /dev/class/block/004
```


## Create the filesystems(创建文件系统)


And now create the minfs and blobstore filesystems on the new partitions:
在新分区中，创建minfs和blobstore文件系统

```
$ mkfs /dev/class/block/003 minfs
$ mkfs /dev/class/block/004 blobstore
```


You're done. Now reboot and pass `-d` to `frun` to run with peristent disks.

现在，已经完成创建，需要重启并`frun` `-d`运行虚拟磁盘

## Keeping a spare(保持配用)


Sometimes you will want to make a fresh clean disk image. After you have created
a new, empty `blk.bin` you can gzip it as a backup:

有时，你会想创建一张干净的磁盘映象。创建完一个新的以后，使用gzip压缩空白的`blk.bin`作为备用。

```
$ gzip blk.bin
```
Then to get a decompressed blk.bin without getting rid of your gzipped backup:

然后，获取到压缩的`blk.bin`，解压缩可以这么做：
```
$ gunzip -k blk.bin.gz
```


## Multiple instances(多个实例)


If you want to run multiple instances of QEMU they must not be trying to use the
same disk image. Each much have their own disk image. You can follow the
instructions above but pass `-D other-disk-name.bin` to `mrun` and `frun` to
specify the alternative location.

如故你想运行多个QEMU 实例，它们不能使用相同的虚拟磁盘。每个都有自己的虚拟磁盘。可以按照上面的说明，传递给 `mrun` 和 `frun`的`-D other-disk-name.bin`用来指定取代位置。
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTkxMzQ3MDc5MCw5NjU3NDUyMjYsMTI1ND
c5Mzg0OV19
-->