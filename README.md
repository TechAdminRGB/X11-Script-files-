Alright gang we tell you how to Cleanly install Packages configured etc, Remember its only for Snapdragon And MediaTek Yet tho there is no Exynos one Alright im yapping again so heres the instruction 

## Step 1: Installing the packagess (Snapdragon) 

First Install The Turnip Drivers

```bash
termux-setup-storage
pkg update && pkg upgrade -y
pkg install x11-repo tur-repo -y
pkg install termux-x11-nightly pulseaudio python python-tkinter \
  qemu-system-x86_64 mesa-zink mesa-vulkan-icd-freedreno \
  virglrenderer-mesa-zink vulkan-loader-android -y
```

What does those ALL Packages does 

termux-setup-storage
 
Explain: This allows Termux to Get Storage Perms So it can write and And move your ISO Files 

Bonus: If you don't know about -y and -n -y means when it asks you like *This Archive will download 50MB etc* So Instead OF YOU Typing y or n -y tells it to auto yes and -n makes it auto no 


x11-repo: This Package is VERY CRITICAL Beacuse Its required for SocketListners those are to hear x11 signals And when caught it will start it Without It Termux dosent even know if x11 even Exists

(Quick note: im getting Lazy typing everything so I will explain those are important bc Im seriously writing this 10 at night) 

python python-tkinter: This installs Python language because this Script has Lines that Python understand without it will Fail 

qemu-system-x86_64: Star of the show here This is the translator if you are running windows x86 or x64 this is the translator it will take the code and translate the code that your CPU Understands

mesa-zink mesa-vulkan-icd-freedreno
  virglrenderer-mesa-zink vulkan-loader-android: this are Turnip and Zink They Are Snapdragon Offcial Drivers optimized makes your booting much faster 

vulkan-loader-android: This Pkg Loads Android built in Vulkan Drivers 


