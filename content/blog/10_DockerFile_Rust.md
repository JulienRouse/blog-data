---
title: 'blabla'
url: '/blog/dockerfile_and_rust'
date: Sat, 09 May 2020 19:00:00 +0000
draft: true
tags: [test-automation,quality,mutation-testing,rust,docker]
featured: false
toc: true
summary: 'Trying my hand at wasm'
---

# Docker file for Rust

## Steps

- open [docker currirculum](https://docker-curriculum.com/)
- open [docker toolbox](toto)
- `docker run hello-world`
- remember that docker is not saving image in the right place (because not much disk space left)
- SO https://stackoverflow.com/questions/42250222/what-is-docker-image-location-on-windows-10, change \$MACHINE_STORAGE_SPACE, go to Virtualbox, delete the default machine, restart docker toolbox
- `docker pull busybox` to follow the tuto
- `docker images` to get all the images
- `docker run busybox` to run the image name busybox, nothing happen.
- `docker run busybox echo 'hello'` to see 'hello' printed
- `docker ps -a` to see all containers that we ran.
- `docker rm [ID]` to delete a container we don't neet anymore
- Write a Dockerfile
-
- (for GUI) install VcXsrv Windows X Server from https://cuneyt.aliustaoglu.biz/en/running-gui-applications-in-docker-on-windows-linux-mac-hosts/
- run `ipconfig` to get the IP address (or 192.168.99.101 like docker took tell)
- `docker run --rm -it -e DISPLAY=192.168.99.1:0.0 julienrouse/roguelike`

## Rust game with bracket-lib (formerly known as RLTK)

### Run locally with WASM target

First of all installing `wasm-bindgen` cli with `cargo install wasm-bindgen-cli`. Then build toward wasm and run it locally on a webpage with the following command (I'm at the root of my project):

- `cargo build --release --target wasm32-unknown-unknown --release`

- `wasm-bindgen ./target/wasm32-unknown-unknown/release/rltk-roguelike.wasm --out-dir . --no-modules --no-typescript` (here replace _rltk-roguelike_ with the name of your project, and I use `--out-dir .` to generate them in the root directory of my project but you can choose a path of your liking)

- You create an index.html with this code

  ```html
  <html>
    <head>
      <meta content="text/html;charset=utf-8" http-equiv="Content-Type" />
    </head>
    <body>
      <canvas id="canvas" width="640" height="480"></canvas>
      <script src="./rltk-roguelike.js"></script>
      <script>
        window.addEventListener('load', async () => {
          await wasm_bindgen('./rltk-roguelike_bg.wasm');
        });
      </script>
    </body>
  </html>
  ```

  You can replace _rltk-roguelike.js_ and _rltk-roguelike_bg.wasm_ by the name of the js generated at the step below

- If you try to open your index.html in your web browser, most likely it won't run because of CORS policy. To bypass that, launch `python -m SimpleHTTPServer` in the directory of the _index.html_ and open `localhost:8000` in my browser

You can try it here

{{% roguelike %}}
