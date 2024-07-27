# Cannot resolve `.local`

I setup a local server on a Raspberry Pi. It is supposed to be available at `rpi.local`. I could connect to the server from my phone, from Windows, and from the terminal. I could not connect from Firefox.

I am using the Bazzite distro at the moment. Firefox or Chromium installed via Discover did not connect to `rpi.local`. It turns out Discover downloads flatpaks and flatpaks do not play nice with mDNS.

I installed the non-flatpak version of chromium and it was able to connect to `rpi.local`. Yay. Problem solved.