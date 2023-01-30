# Lab: Secure Shell

## Required VMs

* VN1-SRV1
* VN1-SRV5
* CL1

## Setup

1. On **CL1**, sign in as **ad\Administrator**.
1. On **VN1-SRV5**, sign in as **ad\Administrator**.
1. In SConfig, enter **15**.

## Introduction

For secure remote command line administration, Adatum wants to deploy the proven SSH protocol. For added security, Adatum wants to use key-based authentication.

## Exercises

1. [Installing and verifying OpenSSH server](#exercise-1-installing-and-verifying-openssh-server)
1. [Configuring key-based authentication](#exercise-2-configuring-key-based-authentication)

## Exercise 1: Installing and verifying OpenSSH server

1. [Install OpenSSH server](#task-1-install-openssh-server) on VN1-SRV5.
1. [Using SSH with a password connect to server](#task-2-using-ssh-with-a-password-connect-to-server) VN1-SRV5

### Task 1: Install OpenSSH server

Perform this task on VN1-SRV5.

1. Install the OpenSSH server.

    ````powershell
    Get-WindowsCapability -Online -Name OpenSSH.Server* |
    Add-WindowsCapability -Online
    ````

1. Start the **sshd** service.

    ````powershell
    $name = 'sshd'
    Start-Service -Name $name
    ````

1. Configure the **sshd** service to start automatically.

    ````powershell
    Set-Service -Name $name -StartupType 'Automatic'
    ````

### Task 2: Using SSH with a password connect to server

Perform this task on CL1.

1. Open **Terminal**.
1. Connect to VN1-SRV5 using ssh.exe.

    ````powershell
    ssh vn1-srv5.ad.adatum.com -l 'Administrator@ad.adatum.com'
    ````

1. At the prompt Are you sure you want to continue connecting, enter **yes**.
1. At the prompt administrator@ad.adatum.com@vn1-srv5.ad.adatum.com's password, enter the password for **Administrator@ad.adatum.com**.

    You are now connected to a cmd.exe session on VN1-SRV5.

1. Confirm the hostname.

    ````shell
    hostname
    ````

    This should return VN1-SRV5.

1. Start PowerShell.

    ````shell
    powershell
    ````

1. Get computer info.

    ````powershell
    Get-ComputerInfo
    ````

1. Exit from PowerShell.

    ````powershell
    exit
    ````

1. Exit from cmd.exe.

    ````shell
    exit
    ````

## Exercise 2: Configuring key-based authentication

1. [Generate user key](#task-1-generate-user-key)
1. [Deploy the public key](#task-2-deploy-the-public-key) to VN1-SRV5
1. [Verify connectivity with the key file](#task-3-verify-connectivity-with-the-key-file)
1. [Store the private key in the Windows security context](#task-4-store-the-private-key-in-the-windows-security-context) on CL1
1. [Verify the connectivity with ssh-agent](#task-5-verify-the-connectivity-with-ssh-agent)

*Important*: In this lab, do not delete the private key file yet! You will need it in a later lab.


### Task 1: Generate user key

Perform this task on CL1.

1. Open **Terminal**.
1. Generate key files using the **Ed25519** algorithm.

    ````powershell
    ssh-keygen.exe -t ed25519 -f '.ssh\Administrator@ad.adatum.com'
    ````

1. At the prompt Enter passphrase and Enter same passphrase again, enter a secure password of your choice. Make sure you write down the passphrase in a secure location.

### Task 2: Deploy the public key

Perform this task on CL1.

1. Open **Terminal**
1. Use sftp to connect to **VN1-SRV5**.

    ````powershell
    sftp.exe Administrator@ad.adatum.com@vn1-srv5.ad.adatum.com
    ````

1. At the prompt Administrator@ad.adatum.com@vn1-srv5.ad.adatum.com's password, enter the password of **Administrator@ad.adatum.com**.

1. Using sftp, from CL1, copy **.ssh/Administrator@ad.adatum.com.pub** to **c:/programdata/ssh/administrators_authorized_keys** on VN1-SRV5.

    ````shell
    put .ssh/Administrator@ad.adatum.com.pub c:/programdata/ssh/administrators_authorized_keys
    ````

1. Quit sftp.

    ````shell
    bye
    ````

1. Connect to VN1-SRV5 using ssh.exe.

    ````powershell
    ssh vn1-srv5.ad.adatum.com -l 'Administrator@ad.adatum.com'
    ````

1. At the prompt administrator@ad.adatum.com@vn1-srv5.ad.adatum.com's password, enter the password for **Administrator@ad.adatum.com**.

1. Grant **Administrators** and **SYSTEM** full control over **C:\\ProgramData\\ssh\\administrators_authorized_keys**.

    ````shell
    icacls.exe c:\ProgramData\ssh\administrators_authorized_keys /inheritance:r /grant "Administrators:F" /grant "SYSTEM:F"
    ````

1. Exit from the ssh session.

    ````shell
    exit
    ````

### Task 3: Verify connectivity with the key file

Perform this task on CL1.

1. Open **Terminal**.
1. Connect to **vn1-srv1.ad.adatum.com** using the private key file **~\.ssh\Administrator@ad.adatum.com**.

    ````powershell
    ssh.exe vn1-srv5.ad.adatum.com -i "~\.ssh\Administrator@ad.adatum.com"
    ````

    There should be no password prompt.

1. Confirm the user name you are connected with.

    ````shell
    whoami
    ````

    The output should be ad\Administrator.

1. Exit from the ssh session.

    ````shell
    exit
    ````

### Task 4: Store the private key in the Windows security context

Perform this task on CL1.

1. Open **Terminal**.
1. Configure the service ssh-agent to start automatically and start it.

    ````powershell
    $name = 'ssh-agent'
    Set-Service -Name $name -StartupType Automatic
    Start-Service -Name $name
    ````

1. Load your key files into ssh-agent.

    ````powershell
    ssh-add.exe "~\.ssh\Administrator@ad.adatum.com"
    ````

### Task 5: Verify the connectivity with ssh-agent

Perform this task on CL1.

1. Open **Terminal**.
1. Connect to **vn1-srv1.ad.adatum.com**.

    ````powershell
    ssh.exe vn1-srv5.ad.adatum.com
    ````

    There should be no password prompt.

1. Confirm the user name you are connected with.

    ````shell
    whoami
    ````

    The output should be ad\Administrator.

1. Exit from the ssh session.

    ````shell
    exit
    ````

In a real-world scenario, it is strongly recommended that you back up your private key to a secure location, then delete it from the local system, after adding it to ssh-agent. The private key cannot be retrieved from the agent providing a strong algorithm has been used, such as Ed25519 in this example. If you lose access to the private key, you will have to create a new key pair and update the public key on all systems you interact with.