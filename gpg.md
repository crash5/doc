# Generate

```bash
export GNUPGHOME=$(mktemp -d)
wget -O $GNUPGHOME/gpg.conf https://raw.githubusercontent.com/drduh/config/master/gpg.conf


gpg --expert --full-generate-key
# certity capatiblity only
export KEYID=0x....

gpg --expert --edit-key $KEYID
    addkey
    ... 
    ...
    adduid
    ...
    trust
        5
    uid 1
        primary
    save

gpg -K
# gpg --export $KEYID | hokey lint

gpg --armor --export-secret-keys $KEYID > $GNUPGHOME/mastersub.key
gpg --armor --export-secret-subkeys $KEYID > $GNUPGHOME/sub.key
gpg --gen-revoke $KEYID --output $GNUPGHOME/revoke.asc
gpg --armor --export $KEYID | sudo tee $GNUPGHOME/gpg-$KEYID-$(date +%F).txt
gpg --export-ssh-key $KEYID

# cp -avi $GNUPGHOME /mnt/encrypted-storage/
# gpg --keyserver pgp.mit.edu --send-key $KEYID

gpg --card-edit
    admin
        passwd
    name
    lang
    login
    list
    quit

gpg --edit-key $KEYID
    key 1
        keytocard
    key 1
    key 2
        keytocard
    ...
    save

gpg -K
```


# Usage

```bash
gpg --import /mnt/0x*txt
# gpg --recv $KEYID

gpg --edit-key $KEYID
    trust
        5

gpg --card-status


# echo "test message string" | gpg --encrypt --armor --recipient $KEYID -o encrypted.txt
# gpg --decrypt --armor encrypted.txt
# echo "test message string" | gpg --armor --clearsign > signed.txt
# gpg --verify signed.txt
# secret () {
#         output=~/"${1}".$(date +%s).enc
#         gpg --encrypt --armor --output ${output} -r 0x0000 -r 0x0001 -r 0x0002 "${1}" && echo "${1} -> ${output}"
# }
# reveal () {
#         output=$(echo "${1}" | rev | cut -c16- | rev)
#         gpg --decrypt --output ${output} "${1}" && echo "${1} -> ${output}"
# }

cd ~/.gnupg
wget https://raw.githubusercontent.com/drduh/config/master/gpg-agent.conf

# export GPG_TTY="$(tty)"
# export SSH_AUTH_SOCK="/run/user/$UID/gnupg/S.gpg-agent.ssh"
# gpg-connect-agent updatestartuptty /bye > /dev/null

export GPG_TTY="$(tty)"
export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)
gpgconf --launch gpg-agent

```
