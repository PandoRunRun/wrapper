# wrapper

A tool to decrypt Apple Music songs. An active subscription is still needed.

Supports only x86_64 and arm64 Linux.

## Installation

Installation methods:

- [Docker](#docker) (recommended)
- Prebuilt binaries (from [releases](https://github.com/PandoRunRun/wrapper/releases) or [actions](https://github.com/PandoRunRun/wrapper/actions))
- [Build from source](#build-from-source)

### Docker

You can easily build the Docker image locally and run it.

1. Clone the repository (Important: must include submodules):

```bash
git clone --recursive https://github.com/PandoRunRun/wrapper.git
cd wrapper
```
*(If you already cloned it without submodules, run `git submodule update --init --recursive`)*

2. Build the image:

```bash
docker build --tag wrapper:local .
```

3. Login to your Apple Music account:

```bash
docker run --privileged --rm -it -v ./rootfs/data:/app/rootfs/data --entrypoint ./wrapper wrapper:local -L "username:password" -H 0.0.0.0
```

Wait until you see `[+] account info cached successfully`, then quit (using `Ctrl-C`).

4. Run the wrapper daemon:

```bash
docker run -d \
  --name am-wrapper \
  --restart=always \
  --privileged \
  -v ./rootfs/data:/app/rootfs/data \
  -p 10020:10020 \
  -p 20020:20020 \
  -p 30020:30020 \
  -e args="-H 0.0.0.0" \
  wrapper:local
```
*(Note: `--restart=always` is recommended. If the Playback Lease expires, the container will automatically restart and re-establish the connection.)*

### Build from source

1. Install dependencies:

- Build tools:

  ```
  sudo apt install build-essential cmake curl unzip git
  ```

- LLVM:

  ```
  sudo bash -c "$(wget -O - https://apt.llvm.org/llvm.sh)"
  ```

- Android NDK r23b:
  ```
  curl -fLO https://dl.google.com/android/repository/android-ndk-r23b-linux.zip
  unzip -d . android-ndk-r23b-linux.zip
  ```

2. Build:

```
git clone https://github.com/WorldObservationLog/wrapper
cd wrapper
mkdir build
cd build
cmake ..
make -j$(nproc)
```

## Usage

```
Usage: wrapper [OPTION]...

  -h, --help              Print help and exit
  -V, --version           Print version and exit
  -H, --host=STRING         (default=`127.0.0.1')
  -D, --decrypt-port=INT    (default=`10020')
  -M, --m3u8-port=INT       (default=`20020')
  -A, --account-port=INT    (default=`30020')
  -P, --proxy=STRING        (default=`')
  -L, --login=STRING        (username:password)
  -F, --code-from-file      (default=off)
```

## Special thanks

- Anonymous, for providing the original version of this project and the legacy Frida decryption method.
- chocomint, for providing support for arm64 arch.
