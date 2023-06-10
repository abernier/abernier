- Enable ssh on your mac: `System preferences > Sharing > Remote Login`
  <img width="827" alt="image" src="https://github.com/abernier/abernier/assets/76580/8df7d200-f522-433e-9a22-6bcbf9d21d86">
- Install [iSH](https://ish.app/) app

Get your local ip, here: `170.20.10.2`

From iSH:
```sh
aberniers-iPhone:~# ssh-keygen -t rsa
aberniers-iPhone:~# ssh-copy-id -i ~/.ssh/id_rsa.pub abernier@170.20.10.2
aberniers-iPhone:~# echo "alias mpb='ssh abernier@170.20.10.2'"
aberniers-iPhone:~# mpb
mbp2:~ abernier$ 
```
