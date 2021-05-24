# Security key stored on the hardware key, with name ssh:lesser and 250 KDF rounds (eg: yubikey)
ssh-keygen -t ed25519-sk -a 250 -Oresident -Oapplication=ssh:lesser -f ~/.ssh/ed25519_sk_lesser

# Load key to ssh agent from the device
ssh-add -K