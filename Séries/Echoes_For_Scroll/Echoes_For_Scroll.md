# Echoes For Scroll

## Approach
Open the provided final.png file and analyze it. Firstly, we can type `file` and `binwalk` commands in order to see the nature of file.

```bash
$ file final.png 
final.png: JPEG image data, comment: "https://www.tumblr.com/ganymede-laughlin", progressive, precision 8, 5376x3584, components 3
```
The output of command file shows that `final.png` contains a comment(a link) in its metadata. This link take us to the profile of *Ganymede Laughlin*
We can see that *Ganymede* is a cybersecurity engineer and he talks about the strength of good password. In second post, we can see his name, date of birth, nickname... 

According to the challenge statement, final.png would contain a hidden file. So, we can use binwalk to see that:

```bash
└──╼ $binwalk final.png

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
4120470       0x3EDF96        Zip archive data, at least v2.0 to extract, name: check/
4120538       0x3EDFDA        Zip archive data, encrypted at least v2.0 to extract, name: check/img_4.png
.            .                .
.            .                .
.            .                .
4219595       0x4062CB        Zip archive data, encrypted at least v2.0 to extract, name: check/img_3986.png
4219816       0x4063A8        Zip archive data, encrypted at least v2.0 to extract, name: check/img_3995.png
4259578       0x40FEFA        End of Zip archive, footer length: 22
```

The output of `binwalk` shows that, there is effectively a hidden data in final.png file. So, we can extract them with the following command:

```bash
└──╼ $binwalk -e final.png 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
4120470       0x3EDF96        Zip archive data, at least v2.0 to extract, name: check/
4120538       0x3EDFDA        Zip archive data, encrypted at least v2.0 to extract, name: check/img_4.png
4120763       0x3EE0BB        Zip archive data, encrypted at least v2.0 to extract, name: check/img_7.png
.            .                .
.            .                .
.            .                .
4219816       0x4063A8        Zip archive data, encrypted at least v2.0 to extract, name: check/img_3995.png
4259578       0x40FEFA        End of Zip archive, footer length: 22

┌─[belmar@parrot]─[~/Desktop/nextgen/series]
└──╼ $ls
final.png  _final.png.extracted
┌─[belmar@parrot]─[~/Desktop/nextgen/series]
└──╼ $cd _final.png.extracted/
┌─[belmar@parrot]─[~/Desktop/nextgen/series/_final.png.extracted]
└──╼ $ls -la
total 136
drwxr-xr-x 1 belmar belmar     30  8 nov.  16:28 .
drwxr-xr-x 1 belmar belmar     58  8 nov.  16:28 ..
-rw-r--r-- 1 belmar belmar 139130  8 nov.  16:28 3EDF96.zip
drwxr-xr-x 1 belmar belmar   9330 31 oct.  02:39 check
┌─[belmar@parrot]─[~/Desktop/nextgen/series/_final.png.extracted]
└──╼ $unzip 3EDF96.zip 
Archive:  3EDF96.zip
   skipping: check/img_4.png         unsupported compression method 99
   skipping: check/img_7.png         unsupported compression method 99
   skipping: check/img_9.png         unsupported compression method 99

```

`binwalk` extracted 3EDF96.zip. If we attempt to unzip the zip file, we can see that the file is protected with password. According to the hint of challenge (`What about using Cupp dictionary to create a wordlist based on the informations ?`), we can use `cupp` to create a wordlist in order to bruteforce the zip file. This wordlist will be based on *Ganymede* profile. 
If you don't have `cupp`, you can install it in your virtual environment with the command: `pip install cupp`

```bash

```

We can then use `zip2john` and `john` to crack the zipfile.