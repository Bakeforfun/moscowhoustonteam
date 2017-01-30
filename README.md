# moscowhoustonteam
Moscow-Houston Data Science Collaboration

## Instructions
### Amazon EC2 server initialisation
1. Request a spot instance
  * For fast usual-purpose computing use m4.16xlarge (64 vCPU, 256 GiB, EBS only)
  * Make sure *Ubuntu Server* is selected, not *Amazon Linux AMI*
  * Create a new key pair or choose an existing one
  * Select a pre-defined security group with open 22 (SSH), 80 (HTTP), 443 (HTTPS), 8888 ports
  * If a new key pair has been created make sure to make the key publicly viewable for SSH to work by using this command:
  ```
  chmod 400 AWS_key_ubuntu.pem
  ```
2. Connect to your instance using its Public DNS (e.g. **ec2-35-156-178-53.eu-central-1.compute.amazonaws.com**):
  ```
  ssh -i "AWS_key_ubuntu.pem" ubuntu@ec2-35-156-178-53.eu-central-1.compute.amazonaws.com
  ```
3. [optional] It is not convenient to use the key pair every time you want to log into your server, especially if you plan to use different devices. Hence I advise to set up a new user and turn on password authentication:
  * Create a new user *brian* (or your choice) using the command:
  ```
  sudo adduser brian
  ```
  * Follow the prompts to enter the password and other optional user information. Usually I just fill in my full user name and leave all other user information fields blank.
  * Add the new user to the sudoers file. Use the *visudo* command:
  ```
  sudo visudo
  ```
  * Add the following line after the comment line, "User privilege specification":
  ```
  brian   ALL=(ALL:ALL) ALL
  ```
  * Save the file by typing *ctlr-X -> y -> Enter*
  * Execute the following command to ensure that *brian* user is in the sudo groups, and so is an administrator:
  ```
  sudo adduser brian sudo
  ```
  * Now, switch to the new user account, *brian*:
  ```
  sudo su brian
  ```
  * You now need to configure the the *sshd_config* file (*sshd_config* is the configuration file for the OpenSSH server. *ssh_config* is the configuration file for the OpenSSH client. Make sure not to get them mixed up). First, make a backup of your *sshd_config* file by copying it to your home directory, or by making a read-only copy in */etc/ssh* by doing (enter your newly-generated password when prompted):
  ```
  sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.factory-defaults
  sudo chmod a-w /etc/ssh/sshd_config.factory-defaults
  ```
  * Once you've backed up your *sshd_config* file, you can make changes with any text editor, for example:
  ```
  sudo vim /etc/ssh/sshd_config
  ```
  * If you have never used *vim* before press *a* to enter the text-edit mode. Press *Esc* to exit it and get back to normal mode. To save and exit type `:wq!` in normal mode and press *Enter*.
  * [very optional] If you have firewalls blocking your 22 port you may change it to 443 which stands for HTTPS and is not normally blocked, otherwise you wouldn't have internet access. Do not however forget to use 443 port instead of 22 when connecting to your server via SSH.
  * Scroll down and find the line `PasswordAuthentication no`. Change it to `PasswordAuthentication yes`
  * Save and exit. Restart the SSH:
  ```
  sudo service ssh restart
  ```
  * You can now connect to your server just by typing and entering the password:
  ```
  ssh brian@ec2-35-156-177-54.eu-central-1.compute.amazonaws.com
  ```
4. Install git (*-y* option omits the confirmation part):
  ```
  sudo apt-get -y install git
  ```
5. Clone this repository:
  ```
  git clone https://github.com/Bakeforfun/moscowhoustonteam.git
  ```
6. Change directory:
  ```
  cd moscowhoustonteam/scripts
  ```
7. The to-be-run script assumes that you use *brian* username. If this is not true, open the script in the editor:
  ```
  vim initialisation-brian.sh
  ```
8. In line 6 (`echo 'PATH="/home/brian/anaconda3/bin:$PATH"' >> .bashrc`) replace *brian* with your username.
9. Run the script by sourcing it (make sure to use *.* not *bash* otherwise the code will be run in a different bash session):
  ```
  . initialisation-brian.sh
  ```
10. This script install *Anaconda* package, C++ compiler, *xgboost* package, compiles it and configures *jupyter* server access. You will be prompted to generate a password for jupyter server accesss and enter some information (which is not necessary).
11. It is better to run the server in a *tmux* session to keep the server running after user logout. If there is no error, you can access the notebook server at `https://<your-instance-public-ip>:8888`. Jupyter notebook will appear running on the server. You will be asked to enter you jupyter password:
```
tmux new -s nb
cd ~/moscowhoustonteam
jupyter notebook
```
