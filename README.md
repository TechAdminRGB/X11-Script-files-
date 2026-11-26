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
(Note if Your On Android 14 Or Higher You Must go To Settings > Apps Special Permission > Pres All Files Access Then Allow Termux) 

Bonus: If you don't know about -y and -n -y means when it asks you like *This Archive will download 50MB etc* So Instead OF YOU Typing y or n -y tells it to auto yes and -n makes it auto no 


x11-repo: This Package is VERY CRITICAL Beacuse Its required X11 To even work bc it reads All The files to do it 

(Quick note: im getting Lazy typing everything so I will explain those are important bc Im seriously writing this 10 at night) 

python python-tkinter: This installs Python language because this Script has Lines that Python understand without it will Fail 

qemu-system-x86_64: Star of the show here This is the translator if you are running windows x86 or x64 this is the translator it will take the code and translate the code that your CPU Understands

mesa-zink mesa-vulkan-icd-freedreno
  virglrenderer-mesa-zink vulkan-loader-android: this are Turnip and Zink They Are Community Build I use it for my Phone too LOL And it offloads you GPU For faster booting

vulkan-loader-android: This Pkg Loads Android built in Vulkan Drivers 


## Step: 2 Moving And installing ISO Files to boot 

for x86 and x64 both are same run this commands: ```bash
mv "/yourfile/path/yourOSFilename" "\$HOME/"
mv /yourfile/path/FileName "\$HOME/yourfilename"


Step: 3 How to Setup Windows 🪟 
First Get a qcow2 file And move it to the $HOME Folder Now Boot The iso then it will Boot windows logo blink for a second then it will blink _ means Kernel Is Loading if your device is slow it will take minutes if fast it should done under 2 mins Now it Boot and it will Ask What language set what language you want it to be installed Now Press Install Now it will say Setup Is Starting After it will Ask You where To Install it 

## WFAT
This is QEMU Built in Disk I do not Recommend using this Beacuse it Can Corrupt or Make Your Disk glitchly or Artificating 

## Drive 0 Unallocated Space This IS THE ONE

This Is Your qcow2 file It will download it in And Best Part you can share it like EASY Just Share and Boot in 

Common Modding (if you need) 

To Change Pass find Android Rooter Line and change Android Rooter to whatever pass you can remember then it will use that Pass What you changed it to 

Already Chat We have gived you Instructions Now Take this Cat EOF Command Copy Paste in Termux and auto build your script


 
cat << 'EOF' > /data/data/com.termux/files/usr/bin/StartPC
#!/data/data/com.termux/files/usr/bin/bash

ulimit -n 65536 2>/dev/null || ulimit -n 4096 2>/dev/null

TMPDIR="${TMPDIR:-/data/data/com.termux/files/usr/tmp}"
BOOT_BG="$HOME/.xp_boot_bg.jpg"
STATUS_FILE="${TMPDIR}/boot_status.txt"
DISPLAY_NAME="Root"

mkdir -p "$HOME/storage/shared/XP_Share" 2>/dev/null

CORE_MASK=$(python3 -c '
import glob
freqs = {}
for p in sorted(glob.glob("/sys/devices/system/cpu/cpu[0-9]*/cpufreq/cpuinfo_max_freq")):
    try:
        cid = int(p.split("cpu")[-1].split("/")[0])
        freqs[cid] = int(open(p).read().strip())
    except Exception:
        pass
if freqs:
    m = max(freqs.values())
    big = [str(c) for c, f in sorted(freqs.items()) if f >= m * 0.70]
    print(",".join(big) if big else "4-7")
else:
    print("4-7")
' 2>/dev/null)
CORE_MASK="${CORE_MASK:-4-7}"

cleanup_all() {
  termux-wake-unlock 2>/dev/null
  pkill -9 -f "[x]p_boot_splash.py" 2>/dev/null
  pkill -9 -f "com.termux.x11" 2>/dev/null
  pkill -9 -f "qemu-system" 2>/dev/null
  pkill -9 -f "pulseaudio" 2>/dev/null
  rm -rf "${TMPDIR}/.X11-unix" "${TMPDIR}/.ICE-unix" "${TMPDIR}/.X0-lock" "${TMPDIR}/shm" "${TMPDIR}/xp_boot_splash.py" "$STATUS_FILE" 2>/dev/null
}

abort_x11() {
  echo ""
  echo "❌ ERROR: $1"
  echo "Stopping X11 launch and cleaning up..."
  exit 1
}

trap cleanup_all EXIT INT TERM

read -sp "Enter VMs Password: " pass
echo ""
if [ "$pass" != "Android Rooter" ]; then
  echo "Wrong password!"
  exit 1
fi

OS_LABEL="Loading Windows 10 (Tiny10 — Adreno 640)..."

echo ""
echo "============================================"
echo "  POCKET-PC BIOS POST v2.1 (Snapdragon 855) "
echo "============================================"
sleep 0.2

run_check() {
  printf "  [ .... ] %s" "$1"
  sleep 0.2
  if eval "$2" >/dev/null 2>&1; then
    printf "\r  [  \033[1;32mOK\033[0m  ] %s\n" "$1"
  else
    printf "\r  [ \033[1;31mFAIL\033[0m ] %s\n" "$1"
    echo ""
    echo "❌ Boot Halted: $3"
    exit 1
  fi
}

run_check "Display (Termux:X11)" "command -v termux-x11" "termux-x11 is not installed."
run_check "Audio (PulseAudio)" "command -v pulseaudio" "pulseaudio is not installed."
run_check "Emulator (QEMU x86_64)" "command -v qemu-system-x86_64" "qemu-system-x86_64 is not installed."
run_check "Adreno GPU (/dev/kgsl-3d0)" "[ -e /dev/kgsl-3d0 ]" "Adreno GPU device node missing."
run_check "Disk (Windows 10)" "[ -r '$HOME/Windows 10.qcow2' ] && [ -w '$HOME/Windows 10.qcow2' ]" "Windows 10.qcow2 missing or not writable."

avail_mb=$(python3 -c "import shutil,os; print(shutil.disk_usage(os.path.expanduser('~')).free // (1024*1024))" 2>/dev/null)
run_check "Storage (${avail_mb:-0}MB Free)" "[ '${avail_mb:-0}' -ge 500 ]" "Less than 500MB free storage available."

echo "============================================"
echo "  ✅ ALL CHECKS PASSED — BOOTING TINY10...  "
echo "============================================"
sleep 0.3

if [ ! -f "$HOME/.config/pulse/default.pa" ] || ! grep -q "module-native-protocol-tcp" "$HOME/.config/pulse/default.pa"; then
  mkdir -p "$HOME/.config/pulse" || abort_x11 "Failed to create PulseAudio config directory."
  cp /data/data/com.termux/files/usr/etc/pulse/default.pa "$HOME/.config/pulse/default.pa" 2>/dev/null
  echo "load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1 auth-anonymous=1" >> "$HOME/.config/pulse/default.pa"
fi

cleanup_all
sleep 1.0
termux-wake-lock 2>/dev/null
mkdir -p "${TMPDIR}/.X11-unix" "${TMPDIR}/.ICE-unix" "${TMPDIR}/shm" || abort_x11 "Failed to create X11/shm directories."
chmod 1777 "${TMPDIR}/.X11-unix" "${TMPDIR}/.ICE-unix" "${TMPDIR}/shm"

export XDG_RUNTIME_DIR="${TMPDIR}"
export DISPLAY=:0

# Adreno 640 Turnip + Zink Hardware Acceleration for X11 / QEMU GTK
export MESA_LOADER_DRIVER_OVERRIDE=zink
export GALLIUM_DRIVER=zink
export TU_DEBUG=noconform
export MESA_VK_WSI_PRESENT_MODE=mailbox
export MESA_GL_VERSION_OVERRIDE=4.6
export mesa_glthread=true

termux-x11 :0 -ac >/dev/null 2>&1 &
X11_PID=$!

X11_READY=0
for i in $(seq 1 20); do
  if ! kill -0 "$X11_PID" 2>/dev/null; then
    abort_x11 "termux-x11 server exited before opening socket."
  fi
  if [ -S "${TMPDIR}/.X11-unix/X0" ]; then
    X11_READY=1
    break
  fi
  sleep 0.4
done

[ "$X11_READY" -eq 1 ] || abort_x11 "Termux-X11 socket failed to respond."

am start --user 0 -n com.termux.x11/com.termux.x11.MainActivity >/dev/null 2>&1 || abort_x11 "Failed to launch Termux:X11 Android app."

pulseaudio --start --exit-idle-time=-1 --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1 auth-anonymous=1" >/dev/null 2>&1 || abort_x11 "PulseAudio failed to start."
export PULSE_SERVER=127.0.0.1
for i in $(seq 1 15); do
  pactl info >/dev/null 2>&1 && break
  sleep 0.3
done

ISO_ARGS=""
if [ -f "$HOME/tiny10.iso" ]; then
  ISO_ARGS="-drive id=cd0,if=none,media=cdrom,file=$HOME/tiny10.iso -device ide-cd,drive=cd0,bus=ide.2,bootindex=1"
fi

GDK_BACKEND=x11 DISPLAY=:0 taskset -c "$CORE_MASK" qemu-system-x86_64 \
  -machine q35,hpet=off \
  -m 3G \
  -smp 3,threads=1 \
  -cpu IvyBridge,-tsc-deadline \
  -accel tcg,thread=multi,tb-size=512 \
  -drive file="$HOME/Windows 10.qcow2",format=qcow2,if=ide,cache=writeback,aio=threads \
  -drive file=fat:rw:fat-type=32:"$HOME/storage/shared/XP_Share",format=raw,if=ide \
  $ISO_ARGS \
  -vga std \
  -rtc base=localtime,clock=host \
  -device piix3-usb-uhci -device usb-tablet \
  -audiodev pa,id=snd0,server=127.0.0.1 \
  -device AC97,audiodev=snd0 \
  -net nic,model=e1000 -net user \
  -display gtk,gl=on,full-screen=on,zoom-to-fit=on || abort_x11 "QEMU Windows 10 failed to launch."
EOF
chmod +x /data/data/com.termux/files/usr/bin/StartPC

