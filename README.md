# Signing a Linux Kernel for Secure Boot
A step-by-step guide on how to install and sign a Linux kernel to boot with Secure Boot, because it shouldn't be so hard to have the latest drivers for your machine 

## Table of contents
<!--ts-->
  * [Why](#why)
  * [How](#how)
    * [Installing a Kernel](#Installing-a-Kernel)
    * [Signing a Kernel for Secure Boot](#Signing-a-Kernel-for-Secure-Boot)
  * [Resources](#resources)
  * [Contributing](#contributing)
<!--te-->

# Why
Hardware vendors, such as Intel, have started to disable features if Secure Boot is not enabled, which could leave a Linux distro completely unable to use integrated graphics. On top of that, some drivers are only now provided through new Kernel releases, so there is no easy way to install the drivers you may need. These problems will be more relevant with laptop installations of Linux.

I spent a long time trying to battle against Secure Boot not letting me install new kernels, because you need to sign them images yourself, and the guides I've found on how to do so tend to be aimed towards system administrators who know more than a general Linux user. So I decided to put a clear step-by-step guide because I think everyone deserves the latest Linux kernels.

# How
To cover the process beginning to end we'll separate it into two steps: installing a kernel, and signing it for Secure Boot.

## Installing a Kernel

First you can check which kernel version you are currently using with:

```console
uname -r
```

In your directory of choice create a folder were you can place the files we'll handle during this process.

We can get the kernel files from [Ubuntu's Mainline repository](https://kernel.ubuntu.com/~kernel-ppa/mainline/?C=N;O=D), note that kernels that end with "-rcX" are "Release Candidate" builds that may not be as stable. For a 64-bit architecture (amd64) you can download the following 4 files:

* linux-headers-<version-num>_all.deb
* linux-headers-<version-num>_amd64.deb
* linux-image-<version-num>_amd64.deb
* linux-modules-<version-num>_amd64.deb
 
<p align="center">
 <img src="media/Ubuntu-mainline-kernels.png" width="50%" height="50%" alt="Ubuntu mainline kernel repository for 5.14"/>
</p>


Put all the downloaded packages into a folder, then within that folder install the downloaded files with:

```console
sudo dpkg -i *.deb
```

## Signing a Kernel for Secure Boot
Once the kernel has been installed we can proceed to siging it for Secure Boot.
 
In the same folder we used for installing the kernels, create a file and place the following text (you may also download the file from this repo [here](#here) if you want), and change the text inside "<>":
 
<details>
  <summary>Click to expand!</summary>

```
# This definition stops the following lines failing if HOME isn't defined.
HOME                    = .
RANDFILE                = $ENV::HOME/.rnd 
[ req ]
distinguished_name      = req_distinguished_name
x509_extensions         = v3
string_mask             = utf8only
prompt                  = no

[ req_distinguished_name ]
countryName             = **<YOURcountrycode>**
stateOrProvinceName     = <YOURstate>
localityName            = <YOURcity>
0.organizationName      = <YOURorganization>
commonName              = Secure Boot Signing Key
emailAddress            = <YOURemail>

[ v3 ]
subjectKeyIdentifier    = hash
authorityKeyIdentifier  = keyid:always,issuer
basicConstraints        = critical,CA:FALSE
extendedKeyUsage        = codeSigning,1.3.6.1.4.1.311.10.3.6
nsComment               = "OpenSSL Generated Certificate"
```
</details>
 
# Resources
There are tools available that simplify the installation and management of Linux kernel in your machine, e.g. [Ukuu](https://teejeetech.in/2019/01/20/ukuu-v19-01/), its deprecated free version [Ukuu Github](https://github.com/teejee2008/ukuu), or its maintained open-source fork [Mainline](https://github.com/bkw777/mainline)

Here is a list of different sources I used in making this guide:

Ubuntu's Mainline Kernels:
 * https://kernel.ubuntu.com/~kernel-ppa/mainline/?C=N;O=D

Installing Ubuntu Kernels:
 * https://itsfoss.com/upgrade-linux-kernel-ubuntu/

Signing Kernel:
 * https://ubuntu.com/blog/how-to-sign-things-for-secure-boot
 * https://gloveboxes.github.io/Ubuntu-for-Azure-Developers/docs/signing-kernel-for-secure-boot.html


## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.
