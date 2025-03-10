* Diagnostics
```
sudo watch -n 0.2 vcgencmd pmic_read_adc
sudo watch -n 0.2 vcgencmd measure_volts

Temperature of google-coral:

watch -n 1 cat /sys/class/thermal/thermal_zone0/temp

https://coral.ai/docs/pcie-parameters/#read-the-edge-tpu-temperature
https://github.com/google-coral/edgetpu/issues/102#issuecomment-631442554

```

At least 4A-5A power supply.

Our system: Raspberry 5 8 GB RAM

We used 5A original Raspberry 5 power supply and a 5A UPS:
https://www.dfrobot.com/product-2840.html

M2-hat:
https://52pi.com/products/m01-m-2-2242-e-key-top

**Final solution:**

sudo nano /boot/firmware/config.txt

dtparam=pciex1_gen=1

The device and the used hat is supposed to support gen=2, but when it runs it is very slowly (lastly 1350 ms per inference **vs 6-7 ms for gen=1**).

```

--model test_data/inception_v1_224_quant_edgetpu.tflite

-------RESULTS--------
----INFERENCE TIME----
Note: The first inference on Edge TPU is slow because it includes loading the model into Edge TPU memory.
39.7ms
-------RESULTS--------
[cat3.jpg]
Egyptian cat: 0.36328
tiger cat: 0.30078
tabby, tabby cat: 0.20312
----INFERENCE TIME----
Note: The first inference on Edge TPU is slow because it includes loading the model into Edge TPU memory.
7.0ms
-------RESULTS--------
[9-360.jpg]
hog, pig, grunter, squealer, Sus scrofa: 0.02344
Arctic fox, white fox, Alopex lagopus: 0.01953
coyote, prairie wolf, brush wolf, Canis latrans: 0.01562
----INFERENCE TIME----
Note: The first inference on Edge TPU is slow because it includes loading the model into Edge TPU memory.
6.8ms
-------RESULTS--------
[cat1.jpg]
Egyptian cat: 0.38281
tabby, tabby cat: 0.21484
tiger cat: 0.19531
----INFERENCE TIME----
Note: The first inference on Edge TPU is slow because it includes loading the model into Edge TPU memory.
6.8ms
-------RESULTS--------
[cat2.jpg]
Egyptian cat: 0.70312
tabby, tabby cat: 0.13672
tiger cat: 0.08594
----INFERENCE TIME----
Note: The first inference on Edge TPU is slow because it includes loading the model into Edge TPU memory.
6.8ms
-------RESULTS--------
[cat4.jpg]
tiger cat: 0.42578
tabby, tabby cat: 0.35156
Egyptian cat: 0.17969



------RESULTS-------- [450]
person
  id:     0
  score:  0.671875
  bbox:   BBox(xmin=2, ymin=75, xmax=332, ymax=595)
8.60 ms
-------RESULTS-------- [475]
dog
  id:     17
  score:  0.55859375
  bbox:   BBox(xmin=11, ymin=1, xmax=328, ymax=374)
8.67 ms
-------RESULTS-------- [500]
dog
  id:     17
  score:  0.5
  bbox:   BBox(xmin=1, ymin=1, xmax=330, ymax=396)
8.58 ms

```
Expected benchmark results according to: (per single TPU or both?) (not on a Pi)
..............................| ours
Inception v1 (224x224)	3.4 ms| 6.6 ms
Inception v4 (299x299)	85 ms | 196.2 ms

...
196.2ms
-------RESULTS--------
[cat1.jpg]
Egyptian cat: 0.80469
tabby, tabby cat: 0.12109
tiger cat: 0.03516

https://coral.ai/docs/edgetpu/benchmarks/

Ours has 2: to do - try two in parallel.
Several tests & yolo: Failed to load delegate from libedgetpu.so.1 - it couldn't inference with  two models at once.


...

**Initial Problems**

Sometimes runs, but extremely slowly (inference time 5000 ms (5 runs)), then can't open the device (see errors, dmesg, ...). When the device is plugged out and in programmatically it sometimes started to run again, but after some runs - it stopped again. Once it started to run very fast with inference time of 60-70 ms. However then when trying many inferences in short bursts on video - it "jammed the whole Pi and slowed down the whole computer which became inaccessible.

```
https://pimylifeup.com/raspberry-pi-watchdog/

home/pi/ -->

/pycoral
/example-camera
/tosh2

#cd /home/pi/pycoral/ bash cat.sh

cd /home/pi/pycoral/

Classify several images:
bash cat.sh

Detect or classify frames in video:

bash vid.sh
bash vidclas.sh

nano examples/classify_image.py
nano examples/classify_video.py
nano examples/detect_video.py
nano examples/detect_image.py

Added code in classify_image for a higher verbosity:

from pycoral.pybind._pywrap_coral import SetVerbosity as set_verbosity
set_verbosity(10)

/pycoral/cat.sh
/pycoral/vid.sh

test_data/
--> models
tf2_mobilenet_v1_1.0_224_ptq_edgetpu.tflite
tfhub_tf2_resnet_50_imagenet_ptq_edgetpu.tflite
tf2_mobilenet_v3_edgetpu_1.0_224_ptq.tflite
etc.

* Labels for different models

--labels test_data/imagenet_labels.txt
--labels test_data/coco_labels.txt


wget https://github.com/google-coral/pycoral/releases/download/v2.0.0/tflite_runtime-2.5.0.post1-cp39-cp39-linux_aarch64.whl 
pip install tflite_runtime-2.5.0.post1-cp39-cp39-linux_aarch64.whl 

https://github.com/raspberrypi/libcamera


https://github.com/raspberrypi/rpicam-apps
#instead of libcamera-
sudo apt install rpicam-apps
...


https://github.com/raspberrypi/pykms
sudo apt install -y libkms++-dev libfmt-dev libdrm-dev
pip install rpi-kms



git branch --show-current
git branch

http://archive.raspberrypi.org/debian/dists/bullseye/main/
https://github.com/raspberrypi/pylibcamera/tree/main

sudo apt install -y libcamera-dev
pip install rpi-libcamera
https://github.com/raspberrypi/pylibcamera/blob/main/README.md

System and Date	libcamera Reported Version	Pip Package Version
Raspberry Pi Bookworm 27/01/2025	v0.4.0+50-83cb8101	0.1a8
Raspberry Pi Bookworm 19/11/2024	v0.3.2+99-1230f78d	0.1a7

 System and Date | libcamera Reported Version | Pip Package Version |
| --------------- | -------------------------- | ------------------- |
| Raspberry Pi Bookworm 27/01/2025 | v0.4.0+50-83cb8101 | 0.1a8

https://github.com/raspberrypi/picamera2/issues/503

sudo apt install -y libkms++-dev libfmt-dev libdrm-dev
pip install rpi-kms
sudo apt-get install libcap-dev
pip install picamera2



https://github.com/DeGirum/yolov5-quantization/tree/master
COCO dataset downloads ...



```
https://aallan.medium.com/google-releases-new-efficientnet-edgetpu-models-for-coral-hardware-39c3f3f734bf    2019

https://github.com/DeGirum/yolov5-quantization/tree/master

https://github.com/google-coral/edgetpu/tree/master/test_data



https://github.com/margaretmz/awesome-tensorflow-lite

https://github.com/google-coral/pycoral/blob/master/examples/classify_image.py#L41

https://github.com/google-coral/pycoral/tree/master/examples

https://www.reddit.com/r/VFIO/comments/l5awg0/using_google_coral_mpcie_tpu_in_qemu_vm/

https://coral.ai/docs/edgetpu/faq/#what-is-the-edge-tpu

https://coral.ai/docs/edgetpu/tflite-python/#run-an-inference-with-the-pycoral-api

https://www.reddit.com/r/VFIO/comments/l5awg0/using_google_coral_mpcie_tpu_in_qemu_vm/

https://github.com/google-coral/edgetpu/issues/491

https://serverfault.com/questions/226319/what-does-pcie-aspm-do
https://serverfault.com/questions/810411/setting-pcie-aspm-off-at-runtime

sudo cat /boot/firmware/cmdline.txt
console=serial0,115200 console=tty1 root=PARTUUID=066aa0b4-02 rootfstype=ext4 fsck.repair=yes rootwait quiet splash plymouth.ignore-serial-consoles cfg80211.ieee80

Added:
pcie_aspm=off pcie_port_pm=off

sudo nano /boot/firmware/cmdline.txt 

...

https://github.com/jveitchmichaelis/edgetpu-yolo

git clone https://github.com/jveitchmichaelis/edgetpu-yolo

cd edgetpu-yolo

python3 detect.py -m yolov5s-int8-224_edgetpu.tflite --bench_image

Download Coco dataset (validation)
https://cocodataset.org/#download

wget http://images.cocodataset.org/zips/val2017.zip
800 MB

mkdir data/coco

pi@pi5-coral:~ $ unzip val2017.zip  -d data/coco/images/
Archive:  val2017.zip
   creating: data/coco/
 extracting: data/coco/val2017/000000212226.jpg
 extracting: data/coco/val2017/000000231527.jpg
 extracting: data/coco/val2017/000000578922.jpg
 
'''
cd ~/edgetpu-yolo

python3 detect.py -m yolov5s-int8-224_edgetpu.tflite --bench_coco --coco_path /home/pi/data/coco/images/val2017/ -q  

python3 detect.py -m yolov5s-int8-224_edgetpu.tflite --bench_coco --coco_path /home/pi/data/coco/images/val2017/    
(no q - more verbose)



python3 eval_coco.py --coco_path /home/josh/data/coco/images/val2017/ --pred_pat ./coco_eval/yolov5s-int8-192_edgetpu.tflite_predictions.json --gt_path /home/josh/data/coco/annotations/instances_val2017.json

'''


_Wasn't that easy? You can swap out different models and try other images if you like. You should see an inference speed of around 25 fps with a 224x224 px input model._


```
:~/pycoral $ python3 examples/classify_image.py   --model test_data/mobilenet_v2_1.0_224_inat_bird_quant_edgetpu.tflite   --labels test_data/inat_bird_labels.txt   --input test_data/parrot.jpg
I tflite/edgetpu_manager_direct.cc:453] No matching device is already opened for shared ownership.
I driver/usb/local_usb_device.cc:944] EnumerateDevices: vendor:0x1a6e, product:0x89a
I driver/usb/local_usb_device.cc:979] EnumerateDevices: checking bus[4] port[0]
I driver/usb/local_usb_device.cc:979] EnumerateDevices: checking bus[3] port[0]
I driver/usb/local_usb_device.cc:979] EnumerateDevices: checking bus[2] port[0]
I driver/usb/local_usb_device.cc:979] EnumerateDevices: checking bus[1] port[0]
I driver/usb/local_usb_device.cc:944] EnumerateDevices: vendor:0x18d1, product:0x9302
I driver/usb/local_usb_device.cc:979] EnumerateDevices: checking bus[4] port[0]
I driver/usb/local_usb_device.cc:979] EnumerateDevices: checking bus[3] port[0]
I driver/usb/local_usb_device.cc:979] EnumerateDevices: checking bus[2] port[0]
I driver/usb/local_usb_device.cc:979] EnumerateDevices: checking bus[1] port[0]
I tflite/edgetpu_context_direct.cc:106] USB always DFU: False (default)
I tflite/edgetpu_context_direct.cc:128] USB bulk-in queue capacity: default
I tflite/edgetpu_context_direct.cc:67] Performance expectation: Max (default)
I ./driver/mmio/host_queue.h:266] Starting in normal mode
I driver/kernel/kernel_registers.cc:83] Opening /dev/apex_0. read_only=0
```
**If ERROR:**
```
I tflite/edgetpu_context_direct.cc:401] Failed to open device [Apex (PCIe)] at [/dev/apex_0]: Failed precondition: Device open failed : -1 (Connection timed out)
Traceback (most recent call last):
  File "/home/pi/.pyenv/versions/3.9.16/lib/python3.9/site-packages/tflite_runtime/interpreter.py", line 160, in load_delegate
    delegate = Delegate(library, options)
  File "/home/pi/.pyenv/versions/3.9.16/lib/python3.9/site-packages/tflite_runtime/interpreter.py", line 119, in __init__
    raise ValueError(capture.message)
ValueError

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/home/pi/pycoral/examples/classify_image.py", line 122, in <module>
    main()
  File "/home/pi/pycoral/examples/classify_image.py", line 72, in main
    interpreter = make_interpreter(*args.model.split('@'))
  File "/home/pi/.pyenv/versions/3.9.16/lib/python3.9/site-packages/pycoral/utils/edgetpu.py", line 87, in make_interpreter
    delegates = [load_edgetpu_delegate({'device': device} if device else {})]
  File "/home/pi/.pyenv/versions/3.9.16/lib/python3.9/site-packages/pycoral/utils/edgetpu.py", line 52, in load_edgetpu_delegate
    return tflite.load_delegate(_EDGETPU_SHARED_LIB, options or {})
  File "/home/pi/.pyenv/versions/3.9.16/lib/python3.9/site-packages/tflite_runtime/interpreter.py", line 162, in load_delegate
    raise ValueError('Failed to load delegate from {}\n{}'.format(
ValueError: Failed to load delegate from libedgetpu.so.1
```
**IF SUCCESS:**
```
I driver/kernel/kernel_registers.cc:97] mmap_offset=0x0000000000040000, mmap_size=4096
I driver/kernel/kernel_registers.cc:108] Got map addr at 0x0x7f82af8000
I driver/kernel/kernel_registers.cc:97] mmap_offset=0x0000000000044000, mmap_size=4096
I driver/kernel/kernel_registers.cc:108] Got map addr at 0x0x7f82af7000
I driver/kernel/kernel_registers.cc:97] mmap_offset=0x0000000000048000, mmap_size=4096
I driver/kernel/kernel_registers.cc:108] Got map addr at 0x0x7f82af6000
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x00000000000486f0, value: = 0x0000000000000000
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000487a8, value = 0x0000000000000000
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x0000000000048578, value: = 0x0000000000000010
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 0000007f82af2000 -> 0000000001000000 (1 pages) flags=00000000.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x0000000001000000
I ./driver/mmio/host_queue.h:162] Queue base : 0x7f82af2000 -> 0x0000000001000000 [4096 bytes]
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 0000007f82af3000 -> 0000000001001000 (1 pages) flags=00000000.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x0000000001001000
I ./driver/mmio/host_queue.h:172] Queue status block : 0x7f82af3000 -> 0x0000000001001000 [16 bytes]
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000048590, value = 0x0000000001000000
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000048598, value = 0x0000000001001000
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a0, value = 0x0000000000000100
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000048568, value = 0x0000000000000005
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x0000000000048570, value: = 0x0000000000000001
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x00000000000486d0, value: = 0x0000000000000000
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000044018, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000044158, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000044198, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000441d8, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000044218, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000048788, value = 0x000000000000007f
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x0000000000048788, value: = 0x000000000000007f
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000400c0, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040150, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040110, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040250, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040298, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000402e0, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040328, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040190, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000401d0, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040210, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486e8, value = 0x0000000000000000
I driver/kernel/linux/kernel_event_handler_linux.cc:45] Set event fd : event_id:0 -> event_fd:7,
I driver/kernel/linux/kernel_event_handler_linux.cc:45] Set event fd : event_id:4 -> event_fd:11,
I driver/kernel/linux/kernel_event_handler_linux.cc:45] Set event fd : event_id:5 -> event_fd:12,
I driver/kernel/linux/kernel_event_linux.cc:62] event_fd=7. Monitor thread begin.
I driver/kernel/linux/kernel_event_linux.cc:62] event_fd=11. Monitor thread begin.
I driver/kernel/linux/kernel_event_handler_linux.cc:45] Set event fd : event_id:6 -> event_fd:13,
I driver/kernel/linux/kernel_event_linux.cc:62] event_fd=12. Monitor thread begin.
I driver/kernel/linux/kernel_event_handler_linux.cc:45] Set event fd : event_id:7 -> event_fd:14,
I driver/kernel/linux/kernel_event_linux.cc:62] event_fd=13. Monitor thread begin.
I driver/kernel/linux/kernel_event_handler_linux.cc:45] Set event fd : event_id:8 -> event_fd:15,
I driver/kernel/linux/kernel_event_linux.cc:62] event_fd=14. Monitor thread begin.
I driver/kernel/linux/kernel_event_handler_linux.cc:45] Set event fd : event_id:9 -> event_fd:16,
I driver/kernel/linux/kernel_event_linux.cc:62] event_fd=15. Monitor thread begin.
I driver/kernel/linux/kernel_event_handler_linux.cc:45] Set event fd : event_id:10 -> event_fd:17,
I driver/kernel/linux/kernel_event_linux.cc:62] event_fd=16. Monitor thread begin.
I driver/kernel/linux/kernel_event_handler_linux.cc:45] Set event fd : event_id:11 -> event_fd:18,
I driver/kernel/linux/kernel_event_linux.cc:62] event_fd=17. Monitor thread begin.
I driver/kernel/linux/kernel_event_handler_linux.cc:45] Set event fd : event_id:12 -> event_fd:19,
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486a0, value = 0x000000000000000f
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c0, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486c0, value = 0x0000000000000001
I tflite/edgetpu_context_direct.cc:174] Opening device at /dev/apex_0
I driver/kernel/linux/kernel_event_linux.cc:62] event_fd=18. Monitor thread begin.
I driver/kernel/linux/kernel_event_linux.cc:62] event_fd=19. Monitor thread begin.
----INFERENCE TIME----
Note: The first inference on Edge TPU is slow because it includes loading the model into Edge TPU memory.
I driver/request.cc:47] Adding input "map/TensorArrayStack/TensorArrayGatherV3" with 150528 bytes.
I driver/request.cc:58] Adding output "prediction" with 965 bytes.
I driver/request.cc:167] Request prepared, total batch size: 1, total TPU requests required: 1.
I driver/driver.cc:307] Request [0]: Submitting P0 request immediately.
I driver/driver.cc:369] Request [0]: Need to map parameters.
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 0000007f64778000 -> 8000000000000000 (964 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000000000
I driver/driver.cc:249] Mapped params : Buffer(ptr=0x7f64778000) -> 0x8000000000000000, 3947392 bytes.
I driver/driver.cc:249] Mapped params : Buffer(ptr=(nil)) -> 0x0000000000000000, 0 bytes.
I driver/driver.cc:383] Request [0]: Need to do parameter-caching.
I driver/single_tpu_request.cc:80] [0] Request constructed.
I driver/instruction_buffers.cc:46] InstructionBuffers created.
I driver/package_registry.cc:647] Created new instruction buffers.
I driver/device_buffer_mapper.cc:75] Mapped scratch : Buffer(ptr=(nil)) -> 0x0000000000000000, 0 bytes.
I driver/single_tpu_request.cc:365] MapDataBuffers() done.
I driver/executable_util.cc:187] Linking Parameter: 0x8000000000000000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807e7000 -> 8000000000400000 (3 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000400000
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x55807e7000) -> 0x8000000000400000, 10064 bytes. Direction=1
I driver/single_tpu_request.cc:381] MapInstructionBuffers() done.
I driver/single_tpu_request.cc:478] [0] SetState old=0, new=1.
I driver/single_tpu_request.cc:390] [0] NotifyRequestSubmitted()
I driver/single_tpu_request.cc:478] [0] SetState old=1, new=2.
I driver/single_queue_dma_scheduler.cc:82] Request[0]: Submitted
I driver/single_tpu_request.cc:398] [0] NotifyRequestActive()
I driver/single_tpu_request.cc:478] [0] SetState old=2, new=3.
I driver/single_queue_dma_scheduler.cc:132] Request[0]: Scheduling DMA[0]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x0000000000000001
I driver/single_tpu_request.cc:80] [1] Request constructed.
I driver/single_tpu_request.cc:113] Adding input "map/TensorArrayStack/TensorArrayGatherV3" with 150528 bytes.
I driver/single_tpu_request.cc:187] Adding output "prediction" with 965 bytes.
I driver/instruction_buffers.cc:46] InstructionBuffers created.
I driver/package_registry.cc:647] Created new instruction buffers.
I driver/device_buffer_mapper.cc:75] Mapped scratch : Buffer(ptr=(nil)) -> 0x0000000000000000, 0 bytes.
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 000000558079c000 -> 8000000000440000 (38 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000440000
I driver/device_buffer_mapper.cc:222] Mapped "map/TensorArrayStack/TensorArrayGatherV3" : Buffer(ptr=0x558079cf40) -> 0x8000000000440f40, 150528 bytes. Direction=1
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807f1000 -> 8000000000404000 (1 pages) flags=00000004.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000404000
I driver/device_buffer_mapper.cc:222] Mapped "prediction" : Buffer(ptr=0x55807f1000) -> 0x8000000000404000, 968 bytes. Direction=2
I driver/single_tpu_request.cc:365] MapDataBuffers() done.
I driver/executable_util.cc:93] Linking map/TensorArrayStack/TensorArrayGatherV3[0]: 0x8000000000440f40
I driver/executable_util.cc:93] Linking prediction[0]: 0x8000000000404000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807f3000 -> 8000000000408000 (3 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000408000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 0000005580811000 -> 8000000000480000 (64 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000480000
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x5580811000) -> 0x8000000000480000, 261920 bytes. Direction=1
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x55807f3000) -> 0x8000000000408000, 10224 bytes. Direction=1
I driver/single_tpu_request.cc:381] MapInstructionBuffers() done.
I driver/single_tpu_request.cc:478] [1] SetState old=0, new=1.
I driver/single_tpu_request.cc:390] [1] NotifyRequestSubmitted()
I driver/single_tpu_request.cc:478] [1] SetState old=1, new=2.
I driver/single_queue_dma_scheduler.cc:82] Request[1]: Submitted
I driver/single_tpu_request.cc:398] [1] NotifyRequestActive()
I driver/single_tpu_request.cc:478] [1] SetState old=2, new=3.
I driver/single_queue_dma_scheduler.cc:132] Request[1]: Scheduling DMA[0]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x0000000000000002
I driver/single_queue_dma_scheduler.cc:132] Request[1]: Scheduling DMA[1]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x0000000000000003
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[0]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=11. Monitor thread got num_events=1.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486a8, value = 0x000000000000000e
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x00000000000486d0, value: = 0x0000000000000001
I driver/single_tpu_request.cc:410] [0] NotifyCompletion()
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807e7000 -> 8000000000400000 (3 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000400000, num_pages = 3
I driver/package_registry.cc:658] Returned instruction buffers back to executable reference
I driver/single_tpu_request.cc:478] [0] SetState old=3, new=4.
I driver/single_queue_dma_scheduler.cc:234] Request[0]: Completed
I driver/single_tpu_request.cc:96] [0] Request destroyed.
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[0]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[1]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=11. Monitor thread got num_events=1.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486a8, value = 0x000000000000000e
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x00000000000486d0, value: = 0x0000000000000002
I driver/single_tpu_request.cc:410] [1] NotifyCompletion()
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807f3000 -> 8000000000408000 (3 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000408000, num_pages = 3
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 0000005580811000 -> 8000000000480000 (64 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000480000, num_pages = 64
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 000000558079c000 -> 8000000000440000 (38 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000440000, num_pages = 38
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807f1000 -> 8000000000404000 (1 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000404000, num_pages = 1
I driver/package_registry.cc:658] Returned instruction buffers back to executable reference
I driver/single_tpu_request.cc:478] [1] SetState old=3, new=4.
I driver/single_queue_dma_scheduler.cc:234] Request[1]: Completed
I driver/single_tpu_request.cc:96] [1] Request destroyed.
1303.7ms
I driver/request.cc:47] Adding input "map/TensorArrayStack/TensorArrayGatherV3" with 150528 bytes.
I driver/request.cc:58] Adding output "prediction" with 965 bytes.
I driver/request.cc:167] Request prepared, total batch size: 1, total TPU requests required: 1.
I driver/driver.cc:307] Request [1]: Submitting P0 request immediately.
I driver/single_tpu_request.cc:80] [2] Request constructed.
I driver/single_tpu_request.cc:113] Adding input "map/TensorArrayStack/TensorArrayGatherV3" with 150528 bytes.
I driver/single_tpu_request.cc:187] Adding output "prediction" with 965 bytes.
I driver/package_registry.cc:639] Reusing old instruction buffers.
I driver/device_buffer_mapper.cc:75] Mapped scratch : Buffer(ptr=(nil)) -> 0x0000000000000000, 0 bytes.
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 000000558079c000 -> 8000000000400000 (38 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000400000
I driver/device_buffer_mapper.cc:222] Mapped "map/TensorArrayStack/TensorArrayGatherV3" : Buffer(ptr=0x558079cf40) -> 0x8000000000400f40, 150528 bytes. Direction=1
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807f7000 -> 8000000000440000 (1 pages) flags=00000004.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000440000
I driver/device_buffer_mapper.cc:222] Mapped "prediction" : Buffer(ptr=0x55807f7000) -> 0x8000000000440000, 968 bytes. Direction=2
I driver/single_tpu_request.cc:365] MapDataBuffers() done.
I driver/executable_util.cc:93] Linking map/TensorArrayStack/TensorArrayGatherV3[0]: 0x8000000000400f40
I driver/executable_util.cc:93] Linking prediction[0]: 0x8000000000440000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807f3000 -> 8000000000444000 (3 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000444000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 0000005580811000 -> 8000000000480000 (64 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000480000
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x5580811000) -> 0x8000000000480000, 261920 bytes. Direction=1
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x55807f3000) -> 0x8000000000444000, 10224 bytes. Direction=1
I driver/single_tpu_request.cc:381] MapInstructionBuffers() done.
I driver/single_tpu_request.cc:478] [2] SetState old=0, new=1.
I driver/single_tpu_request.cc:390] [2] NotifyRequestSubmitted()
I driver/single_tpu_request.cc:478] [2] SetState old=1, new=2.
I driver/single_queue_dma_scheduler.cc:82] Request[2]: Submitted
I driver/single_tpu_request.cc:398] [2] NotifyRequestActive()
I driver/single_tpu_request.cc:478] [2] SetState old=2, new=3.
I driver/single_queue_dma_scheduler.cc:132] Request[2]: Scheduling DMA[0]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x0000000000000004
I driver/single_queue_dma_scheduler.cc:132] Request[2]: Scheduling DMA[1]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x0000000000000005
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[0]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[1]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=11. Monitor thread got num_events=1.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486a8, value = 0x000000000000000e
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x00000000000486d0, value: = 0x0000000000000003
I driver/single_tpu_request.cc:410] [2] NotifyCompletion()
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807f3000 -> 8000000000444000 (3 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000444000, num_pages = 3
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 0000005580811000 -> 8000000000480000 (64 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000480000, num_pages = 64
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 000000558079c000 -> 8000000000400000 (38 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000400000, num_pages = 38
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807f7000 -> 8000000000440000 (1 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000440000, num_pages = 1
I driver/package_registry.cc:658] Returned instruction buffers back to executable reference
I driver/single_tpu_request.cc:478] [2] SetState old=3, new=4.
I driver/single_queue_dma_scheduler.cc:234] Request[2]: Completed
I driver/single_tpu_request.cc:96] [2] Request destroyed.
149.5ms
I driver/request.cc:47] Adding input "map/TensorArrayStack/TensorArrayGatherV3" with 150528 bytes.
I driver/request.cc:58] Adding output "prediction" with 965 bytes.
I driver/request.cc:167] Request prepared, total batch size: 1, total TPU requests required: 1.
I driver/driver.cc:307] Request [2]: Submitting P0 request immediately.
I driver/single_tpu_request.cc:80] [3] Request constructed.
I driver/single_tpu_request.cc:113] Adding input "map/TensorArrayStack/TensorArrayGatherV3" with 150528 bytes.
I driver/single_tpu_request.cc:187] Adding output "prediction" with 965 bytes.
I driver/package_registry.cc:639] Reusing old instruction buffers.
I driver/device_buffer_mapper.cc:75] Mapped scratch : Buffer(ptr=(nil)) -> 0x0000000000000000, 0 bytes.
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 000000558079c000 -> 8000000000400000 (38 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000400000
I driver/device_buffer_mapper.cc:222] Mapped "map/TensorArrayStack/TensorArrayGatherV3" : Buffer(ptr=0x558079cf40) -> 0x8000000000400f40, 150528 bytes. Direction=1
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807f9000 -> 8000000000440000 (1 pages) flags=00000004.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000440000
I driver/device_buffer_mapper.cc:222] Mapped "prediction" : Buffer(ptr=0x55807f9000) -> 0x8000000000440000, 968 bytes. Direction=2
I driver/single_tpu_request.cc:365] MapDataBuffers() done.
I driver/executable_util.cc:93] Linking map/TensorArrayStack/TensorArrayGatherV3[0]: 0x8000000000400f40
I driver/executable_util.cc:93] Linking prediction[0]: 0x8000000000440000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807f3000 -> 8000000000444000 (3 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000444000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 0000005580811000 -> 8000000000480000 (64 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000480000
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x5580811000) -> 0x8000000000480000, 261920 bytes. Direction=1
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x55807f3000) -> 0x8000000000444000, 10224 bytes. Direction=1
I driver/single_tpu_request.cc:381] MapInstructionBuffers() done.
I driver/single_tpu_request.cc:478] [3] SetState old=0, new=1.
I driver/single_tpu_request.cc:390] [3] NotifyRequestSubmitted()
I driver/single_tpu_request.cc:478] [3] SetState old=1, new=2.
I driver/single_queue_dma_scheduler.cc:82] Request[3]: Submitted
I driver/single_tpu_request.cc:398] [3] NotifyRequestActive()
I driver/single_tpu_request.cc:478] [3] SetState old=2, new=3.
I driver/single_queue_dma_scheduler.cc:132] Request[3]: Scheduling DMA[0]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x0000000000000006
I driver/single_queue_dma_scheduler.cc:132] Request[3]: Scheduling DMA[1]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x0000000000000007
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[0]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[1]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=11. Monitor thread got num_events=1.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486a8, value = 0x000000000000000e
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x00000000000486d0, value: = 0x0000000000000004
I driver/single_tpu_request.cc:410] [3] NotifyCompletion()
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807f3000 -> 8000000000444000 (3 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000444000, num_pages = 3
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 0000005580811000 -> 8000000000480000 (64 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000480000, num_pages = 64
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 000000558079c000 -> 8000000000400000 (38 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000400000, num_pages = 38
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807f9000 -> 8000000000440000 (1 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000440000, num_pages = 1
I driver/package_registry.cc:658] Returned instruction buffers back to executable reference
I driver/single_tpu_request.cc:478] [3] SetState old=3, new=4.
I driver/single_queue_dma_scheduler.cc:234] Request[3]: Completed
I driver/single_tpu_request.cc:96] [3] Request destroyed.
124.7ms
I driver/request.cc:47] Adding input "map/TensorArrayStack/TensorArrayGatherV3" with 150528 bytes.
I driver/request.cc:58] Adding output "prediction" with 965 bytes.
I driver/request.cc:167] Request prepared, total batch size: 1, total TPU requests required: 1.
I driver/driver.cc:307] Request [3]: Submitting P0 request immediately.
I driver/single_tpu_request.cc:80] [4] Request constructed.
I driver/single_tpu_request.cc:113] Adding input "map/TensorArrayStack/TensorArrayGatherV3" with 150528 bytes.
I driver/single_tpu_request.cc:187] Adding output "prediction" with 965 bytes.
I driver/package_registry.cc:639] Reusing old instruction buffers.
I driver/device_buffer_mapper.cc:75] Mapped scratch : Buffer(ptr=(nil)) -> 0x0000000000000000, 0 bytes.
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 000000558079c000 -> 8000000000400000 (38 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000400000
I driver/device_buffer_mapper.cc:222] Mapped "map/TensorArrayStack/TensorArrayGatherV3" : Buffer(ptr=0x558079cf40) -> 0x8000000000400f40, 150528 bytes. Direction=1
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807fb000 -> 8000000000440000 (1 pages) flags=00000004.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000440000
I driver/device_buffer_mapper.cc:222] Mapped "prediction" : Buffer(ptr=0x55807fb000) -> 0x8000000000440000, 968 bytes. Direction=2
I driver/single_tpu_request.cc:365] MapDataBuffers() done.
I driver/executable_util.cc:93] Linking map/TensorArrayStack/TensorArrayGatherV3[0]: 0x8000000000400f40
I driver/executable_util.cc:93] Linking prediction[0]: 0x8000000000440000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807f3000 -> 8000000000444000 (3 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000444000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 0000005580811000 -> 8000000000480000 (64 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000480000
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x5580811000) -> 0x8000000000480000, 261920 bytes. Direction=1
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x55807f3000) -> 0x8000000000444000, 10224 bytes. Direction=1
I driver/single_tpu_request.cc:381] MapInstructionBuffers() done.
I driver/single_tpu_request.cc:478] [4] SetState old=0, new=1.
I driver/single_tpu_request.cc:390] [4] NotifyRequestSubmitted()
I driver/single_tpu_request.cc:478] [4] SetState old=1, new=2.
I driver/single_queue_dma_scheduler.cc:82] Request[4]: Submitted
I driver/single_tpu_request.cc:398] [4] NotifyRequestActive()
I driver/single_tpu_request.cc:478] [4] SetState old=2, new=3.
I driver/single_queue_dma_scheduler.cc:132] Request[4]: Scheduling DMA[0]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x0000000000000008
I driver/single_queue_dma_scheduler.cc:132] Request[4]: Scheduling DMA[1]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x0000000000000009
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[0]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[1]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=11. Monitor thread got num_events=1.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486a8, value = 0x000000000000000e
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x00000000000486d0, value: = 0x0000000000000005
I driver/single_tpu_request.cc:410] [4] NotifyCompletion()
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807f3000 -> 8000000000444000 (3 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000444000, num_pages = 3
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 0000005580811000 -> 8000000000480000 (64 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000480000, num_pages = 64
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 000000558079c000 -> 8000000000400000 (38 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000400000, num_pages = 38
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807fb000 -> 8000000000440000 (1 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000440000, num_pages = 1
I driver/package_registry.cc:658] Returned instruction buffers back to executable reference
I driver/single_tpu_request.cc:478] [4] SetState old=3, new=4.
I driver/single_queue_dma_scheduler.cc:234] Request[4]: Completed
I driver/single_tpu_request.cc:96] [4] Request destroyed.
122.7ms
I driver/request.cc:47] Adding input "map/TensorArrayStack/TensorArrayGatherV3" with 150528 bytes.
I driver/request.cc:58] Adding output "prediction" with 965 bytes.
I driver/request.cc:167] Request prepared, total batch size: 1, total TPU requests required: 1.
I driver/driver.cc:307] Request [4]: Submitting P0 request immediately.
I driver/single_tpu_request.cc:80] [5] Request constructed.
I driver/single_tpu_request.cc:113] Adding input "map/TensorArrayStack/TensorArrayGatherV3" with 150528 bytes.
I driver/single_tpu_request.cc:187] Adding output "prediction" with 965 bytes.
I driver/package_registry.cc:639] Reusing old instruction buffers.
I driver/device_buffer_mapper.cc:75] Mapped scratch : Buffer(ptr=(nil)) -> 0x0000000000000000, 0 bytes.
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 000000558079c000 -> 8000000000400000 (38 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000400000
I driver/device_buffer_mapper.cc:222] Mapped "map/TensorArrayStack/TensorArrayGatherV3" : Buffer(ptr=0x558079cf40) -> 0x8000000000400f40, 150528 bytes. Direction=1
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807fd000 -> 8000000000440000 (1 pages) flags=00000004.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000440000
I driver/device_buffer_mapper.cc:222] Mapped "prediction" : Buffer(ptr=0x55807fd000) -> 0x8000000000440000, 968 bytes. Direction=2
I driver/single_tpu_request.cc:365] MapDataBuffers() done.
I driver/executable_util.cc:93] Linking map/TensorArrayStack/TensorArrayGatherV3[0]: 0x8000000000400f40
I driver/executable_util.cc:93] Linking prediction[0]: 0x8000000000440000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 00000055807f3000 -> 8000000000444000 (3 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000444000
I driver/kernel/kernel_mmu_mapper.cc:135] MmuMapper#Map() : 0000005580811000 -> 8000000000480000 (64 pages) flags=00000002.
I driver/memory/mmio_address_space.cc:55] MapMemory() page-aligned : device_address = 0x8000000000480000
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x5580811000) -> 0x8000000000480000, 261920 bytes. Direction=1
I driver/device_buffer_mapper.cc:222] Mapped "instructions" : Buffer(ptr=0x55807f3000) -> 0x8000000000444000, 10224 bytes. Direction=1
I driver/single_tpu_request.cc:381] MapInstructionBuffers() done.
I driver/single_tpu_request.cc:478] [5] SetState old=0, new=1.
I driver/single_tpu_request.cc:390] [5] NotifyRequestSubmitted()
I driver/single_tpu_request.cc:478] [5] SetState old=1, new=2.
I driver/single_queue_dma_scheduler.cc:82] Request[5]: Submitted
I driver/single_tpu_request.cc:398] [5] NotifyRequestActive()
I driver/single_tpu_request.cc:478] [5] SetState old=2, new=3.
I driver/single_queue_dma_scheduler.cc:132] Request[5]: Scheduling DMA[0]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x000000000000000a
I driver/single_queue_dma_scheduler.cc:132] Request[5]: Scheduling DMA[1]
I ./driver/mmio/host_queue.h:383] Adding an element to the host queue.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x000000000000000b
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[0]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I ./driver/mmio/host_queue.h:416] Completed 1 elements.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c8, value = 0x0000000000000000
I driver/single_queue_dma_scheduler.cc:154] Completing DMA[1]
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=11. Monitor thread got num_events=1.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486a8, value = 0x000000000000000e
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x00000000000486d0, value: = 0x0000000000000006
I driver/single_tpu_request.cc:410] [5] NotifyCompletion()
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807f3000 -> 8000000000444000 (3 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000444000, num_pages = 3
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 0000005580811000 -> 8000000000480000 (64 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000480000, num_pages = 64
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 000000558079c000 -> 8000000000400000 (38 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000400000, num_pages = 38
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 00000055807fd000 -> 8000000000440000 (1 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000440000, num_pages = 1
I driver/package_registry.cc:658] Returned instruction buffers back to executable reference
I driver/single_tpu_request.cc:478] [5] SetState old=3, new=4.
I driver/single_queue_dma_scheduler.cc:234] Request[5]: Completed
I driver/single_tpu_request.cc:96] [5] Request destroyed.
134.2ms
-------RESULTS--------
Ara macao (Scarlet Macaw): 0.75781
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 0000007f64778000 -> 8000000000000000 (964 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x8000000000000000, num_pages = 964
I driver/instruction_buffers.cc:51] InstructionBuffers destroyed.
I driver/instruction_buffers.cc:51] InstructionBuffers destroyed.
I tflite/edgetpu_manager_direct.cc:226] Releasing Edge TPU device at /dev/apex_0
I tflite/edgetpu_context_direct.cc:180] Closing Edge TPU device at /dev/apex_0
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486d8, value = 0x0000000000000001
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x00000000000486e0, value: = 0x0000000000000001
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000044018, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000044158, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000044198, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000441d8, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000044218, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000048788, value = 0x000000000000007f
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x0000000000048788, value: = 0x000000000000007f
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000400c0, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040150, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040110, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040250, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040298, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000402e0, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040328, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040190, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000401d0, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000040210, value = 0x0000000000000002
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486c0, value = 0x0000000000000000
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485c0, value = 0x0000000000000000
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000486a0, value = 0x0000000000000000
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=7. Monitor thread got num_events=1.
I driver/kernel/linux/kernel_event_linux.cc:85] event_fd=7. Monitor thread exit.
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=11. Monitor thread got num_events=1.
I driver/kernel/linux/kernel_event_linux.cc:85] event_fd=11. Monitor thread exit.
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=12. Monitor thread got num_events=1.
I driver/kernel/linux/kernel_event_linux.cc:85] event_fd=12. Monitor thread exit.
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=13. Monitor thread got num_events=1.
I driver/kernel/linux/kernel_event_linux.cc:85] event_fd=13. Monitor thread exit.
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=14. Monitor thread got num_events=1.
I driver/kernel/linux/kernel_event_linux.cc:85] event_fd=14. Monitor thread exit.
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=15. Monitor thread got num_events=1.
I driver/kernel/linux/kernel_event_linux.cc:85] event_fd=15. Monitor thread exit.
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=16. Monitor thread got num_events=1.
I driver/kernel/linux/kernel_event_linux.cc:85] event_fd=16. Monitor thread exit.
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=17. Monitor thread got num_events=1.
I driver/kernel/linux/kernel_event_linux.cc:85] event_fd=17. Monitor thread exit.
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=18. Monitor thread got num_events=1.
I driver/kernel/linux/kernel_event_linux.cc:85] event_fd=18. Monitor thread exit.
I driver/kernel/linux/kernel_event_linux.cc:75] event_fd=19. Monitor thread got num_events=1.
I driver/kernel/linux/kernel_event_linux.cc:85] event_fd=19. Monitor thread exit.
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000048568, value = 0x0000000000000000
I driver/kernel/kernel_registers.cc:211] Read: offset = 0x0000000000048570, value: = 0x0000000000000000
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x00000000000485a8, value = 0x0000000000000000
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000048590, value = 0x0000000000000000
I driver/kernel/kernel_registers.cc:190] Write: offset = 0x0000000000048598, value = 0x0000000000000000
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 0000007f82af2000 -> 0000000001000000 (1 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x0000000001000000, num_pages = 1
I driver/kernel/kernel_mmu_mapper.cc:171] MmuMaper#Unmap() : 0000007f82af3000 -> 0000000001001000 (1 pages).
I driver/memory/mmio_address_space.cc:82] UnmapMemory() page-aligned : device_address = 0x0000000001001000, num_pages = 1
I driver/kernel/kernel_registers.cc:122] Closing /dev/apex_0. mmap_offset=0x0000000000040000, mmap_size=4096, read_only=0
I driver/kernel/kernel_registers.cc:122] Closing /dev/apex_0. mmap_offset=0x0000000000044000, mmap_size=4096, read_only=0
I driver/kernel/kernel_registers.cc:122] Closing /dev/apex_0. mmap_offset=0x0000000000048000, mmap_size=4096, read_only=0
:~/pycoral $
```


COCO Labels and classes:

https://github.com/ultralytics/yolov5/blob/master/data/coco128.yaml
# Classes
names:
  0: person
  1: bicycle
  2: car
  3: motorcycle
  4: airplane
  5: bus
  6: train
  7: truck
  8: boat
  9: traffic light
  10: fire hydrant
  11: stop sign
  12: parking meter
  13: bench
  14: bird
  15: cat
  16: dog
  17: horse
  18: sheep
  19: cow
  20: elephant
  21: bear
  22: zebra
  23: giraffe
  24: backpack
  25: umbrella
  26: handbag
  27: tie
  28: suitcase
  29: frisbee
  30: skis
  31: snowboard
  32: sports ball
  33: kite
  34: baseball bat
  35: baseball glove
  36: skateboard
  37: surfboard
  38: tennis racket
  39: bottle
  40: wine glass
  41: cup
  42: fork
  43: knife
  44: spoon
  45: bowl
  46: banana
  47: apple
  48: sandwich
  49: orange
  50: broccoli
  51: carrot
  52: hot dog
  53: pizza
  54: donut
  55: cake
  56: chair
  57: couch
  58: potted plant
  59: bed
  60: dining table
  61: toilet
  62: tv
  63: laptop
  64: mouse
  65: remote
  66: keyboard
  67: cell phone
  68: microwave
  69: oven
  70: toaster
  71: sink
  72: refrigerator
  73: book
  74: clock
  75: vase
  76: scissors
  77: teddy bear
  78: hair drier
  79: toothbrush
