# SSH and GPU problems
This file outlines some common problems relating to SSH and GPU (separately) and some tips to deal with them.

## SSH Process Keep Running
Check this webpage, [Google Compute Engine: Keeping Your Process Running After SSH Logout (GNU Screen)](http://orcaman.blogspot.com/2013/08/google-compute-engine-keeping-your.html). Ignore the `gcutil` part, that is outdated. Refer to the [SSH section](README.md/#ssh-to-vm) in README for update details.


Once we SSH into the VM, what if we want to run a certain process - for example, compile / build some code that's going to take a while? or maybe start our Erlang server? 

If we trust ssh then we are bound to fail - the connection will eventually break (we can try to keep it alive by automatic pinging and other methods, but what if we lose connectivity in our office / client?).

The solution: GNU screen. This awesome little tool let's you run a process after you've ssh'ed into your remote server, and then detach from it - leaving it running like it would run in the foreground (not stopped in the background).

So after we've ssh'ed into our GCE VM, we will need to:
1. install GNU screen: 

   - $ `sudo apt-get update`
   - $ `sudo apt-get upgrade`
   - $ `sudo apt-get install screen`


2. Type `screen`. this will open up a new screen - kind of similar in look & feel to what `clear` would result in. 
3. Tun the process (e.g.: `./run.sh` to run a kaldi recipe)
4. Type: `Ctrl + A`, and then `Ctrl + D`. This will detach your screen session but leave your processes running!
5. Feel free to close the SSH terminal. whenever you feel like it, ssh back into your GCE VM, and type `screen -r` to resume your previously detached session.
6. to kill all detached screens, run: 
   - `screen -ls | grep pts | cut -d. -f1 | awk '{print $1}' | xargs kill`

## GPU 

If there is a problem with the GPU saying no gpu is available, check the [GPU Quota](README.md/#increase-gpu-quota) section in README.

If you are having trouble while running the project:

Do the following steps :

1) Change RAM from 32 to 48GB

2) Change number of jobs. For example, in the Tedlium egs, in run_tdnn_1g,  change

`--trainer.optimization.num-jobs-initial 3 \`

`--trainer.optimization.num-jobs-final 12 \`


TO:

`--trainer.optimization.num-jobs-initial 2 \`

`--trainer.optimization.num-jobs-final 3 \`
     or somtimes even to 
`--trainer.optimization.num-jobs-final 2 \`

3) use `nvidia-smi -c 3` (for exclusive mode)

4) append `--use-gpu=wait` to `steps/nnet3/chain/train.py` in `run_tdnn_1g.sh`

