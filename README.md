# Hugo Uploader
> Command line tool for Hugo-based websites FTP differential upload.

[![Python](https://img.shields.io/badge/python-v3.7+-blue.svg)](https://www.python.org)
[![License](https://img.shields.io/github/license/bernarpa/hugo-uploader.svg)](https://opensource.org/licenses/AGPL-3.0)
[![GitHub issues](https://img.shields.io/github/issues/bernarpa/hugo-uploader.svg)](https://github.com/bernarpa/hugo-uploader/issues)

## Table of content

- [Why Hugo Uploader](#why-hugo-uploader)
- [Usage](#usage)
- [How does it work](#how-does-it-work)
- [License](#license)

## Why Hugo Uploader

Even though I love using the [Hugo static site generator](https://gohugo.io/) I've always found the process of uploading compiled websites via FTP rather clumsy.

I've tried both interactive graphical utilities, such as [FileZilla](https://filezilla-project.org/) and simple command line script based on [LFTP](http://lftp.yar.ru/) or [NcFTP](https://www.ncftp.com/), but somehow the results have always been suboptimal with respect to differential upload capabilities (uploading only new or modified files):
  
  - using timestamps to verify which files have changed remotely is often unreliable;
  - using file sizes got me a few headaches in the recent past, let's say that the probability that a modified page retains the same size as before is greater than you might think.

Obviously these issues are caused by limitations of the FTP protocol, the aforementioned tools are great. However, for the specific scenario of static generated websites and FTP I needed a more precise differential uploader. Therefore, I took the occasion to play with Python a bit and here's Hugo Uploader.

## Usage

To generate and upload a Hugo-based website with Hugo Uploader, just run the program on the root directory of the website or pass that directory as command line argument:

    $ cd /hugo/website
    $ hugo-uploader

Alternatively, you may specify the Hugo website root directory as a command line argument:

    $ hugo-uploader /hugo/website

# How does it work

Firstly, Hugo Uploader will invoke the `hugo` command to build a minified version on the website in the `public` directory.

Secondly, if Hugo Uploader was run for the first time, it will ask for FTP connection details. Such details will be stored in the `.hugo-uploader.cfg` file within the website root directory. Please note that this file will contain the cleartext FTP password as well, you might want to keep it away from privy eyes.

Finally, Hugo Uploader will perform FTP upload of all new or changed `public` files in the specified FTP server. If Hugo Uploader was run for the first time it will simply upload each file (one by one, to keep the tool simple I didn't implement parallel connections yet), otherwise it will upload only new or modified items.

In order to find out which files have been added or modified, Hugo Uploaded stores a `.hash-list` file on the website root directory. This file contains a list of all files contained on the `public` directory and their cryptographic hash from the last successful FTP upload.

Please note that storing `.hugo-uploader.cfg` and `.hash-list` on the website root directory works well for my personal workflow, as I keep my Hugo-based websites on private git repositories. Therefore:

  - `.hugo-uploader.cfg` will not be publicly exposed;
  - `.hash-list` will stored in the git repository as part of the website, together with the changes that I will perform from time to time before generating it.

# License

Hugo Uploader is licensed under the terms of the GNU Affero General Public License version 3.