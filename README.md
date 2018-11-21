
Using ansible-vault for username, password, and key location.
Prepare raspberry pi's
Burn 2018-11-13-raspbian-stretch-lite.img to sd card
Boot from sd card and set the wifi and ssh server
  sudo raspi-config
	A. option 2, configure network settings, then option N2 for wi-fi
	B. option 5, interfacing options, then P2, set SSH server to on.
	C. ip addr to get ip address
