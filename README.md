# DataSurgeon (WIP)
DataSurgeon (ds) is a versatile tool designed for incident response, penetration testing, and CTF challenges. It allows for the extraction of various types of sensitive information including emails, credit cards, URLs, IP addresses, MAC addresses, and SRV DNS records.

# Quick Links
* [Features](#features)
* [Quick Install](#quick-install)
* [Command Line Arguments](#command-line-arguments)
* [Examples](#examples)
* [Speed Tests](#speed-tests)
* [Project Goals](#project-goals)

# Features
* Supports Windows, Linux and MacOS
* Accepts files
* Can process standard input
* Extracted information can be output to a secondary file


## Extractable Information 
* Emails
* Files
* Credit Cards
* URL's
* Windows Domain Usernames
* IPv4 Addresses and IPv6 addresses
* MAC Addresses
* SRV DNS Records

### Want more? 
Please read the contributing guidelines [here](https://github.com/Drew-Alleman/DataSurgeon/blob/main/CONTRIBUTING.md#adding-a-new-regex--extraction-feature)

# Quick Install
### Linux
```
wget -O - https://raw.githubusercontent.com/Drew-Alleman/DataSurgeon/main/install/install.sh | bash
```

### Windows 
1. Install Rust [here](https://www.rust-lang.org/tools/install)
2. Install Github [here](https://desktop.github.com/)
3. Enter the line below in an elevated powershell window. 
```
IEX (New-Object Net.WebClient).DownloadString("https://raw.githubusercontent.com/Drew-Alleman/DataSurgeon/main/install/install.ps1")
```
4. Relaunch terminal <br> <br>
When complete you will be able to use ```ds``` from the command line anywhere.

### Mac
```
curl --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/Drew-Alleman/DataSurgeon/main/install/install.sh | sh
```

# Command Line Arguments
```
$ ds -h 
DataSurgeon: https://github.com/Drew-Alleman/DataSurgeon 1.0
Drew Alleman
DataSurgeon (ds) extracts sensitive information from standard input for incident response,
penetration testing, and CTF challenges, including emails, credit cards, URLs, IPs, MAC addresses,
and SRV DNS records.

USAGE:
    ds [OPTIONS]

OPTIONS:
    -6, --ipv6-addr          Extracts IPv6 addresses from the desired file
    -c, --credit-card        Extract credit card numbers
    -d, --dns                Extract Domain Name System records
    -D, --domain-users       Extract possible Windows domain user accounts
    -e, --email              Used to extract email addresses from the specifed file or output stream
    -f, --file <file>        File to extract information from
    -F, --files              Extract filenames
    -h, --help               Print help information
    -i, --ip-addr            Extracts IP addresses from the desired file
    -j, --junk               Attempt to remove some of the junk information that might have been
                             sent back
    -m, --mac-addr           Extract's MAC addresses
    -o, --output <output>    Output's the results of the procedure to a local file (recommended for
                             large files)
    -t, --time               Time how long the operation took
    -u, --url                Extract url's
    -V, --version            Print version information                                                     
```
# Examples
## Extracting Files From a Remote Webiste
Here I use ```wget``` to make a request to stackoverflow then I forward the body text to ds . The ```-F``` option will list all files found in the HTML source ```--junk``` is used to remove any extra text that might have been returned (such as extra html). Then the result of is sent to uniq which removes any non unique files found.
```
wget -qO - https://www.stackoverflow.com | ds -F --junk | uniq                                                                                      
files: apple-touch-icon.png
files: opensearch.xml
files: 2.png
files: min.js
files: en.js
files: .js
files: min.js
files: StackExchange.ini
files: en.js
files: gps.ini
files: topbar.ini
files: illo-question.png
files: illo-for-you.png
files: illo-home-search.png
files: e.tar
files: illo-integrations-left.png
files: illo-integrations-right.png
files: apple-touch-icon.png
files: gtag.js
files: ga.i
```

## Extracting Mac Addresses From an Output File
Here I am pulling all mac addresses found in [autodeauth's](https://github.com/Drew-Alleman/autodeauth) log file using the ```-m``` query.
```
$ ./ds -m -f /var/log/autodeauth/log     
mac_address: 2023-02-26 00:28:19 - Sending 500 deauth frames to network: BC:2E:48:E5:DE:FF -- PrivateNetwork
mac_address: 2023-02-26 00:35:22 - Sending 500 deauth frames to network: 90:58:51:1C:C9:E1 -- TestNet
mac_address: 2023-02-26 00:35:40 - Sending 500 deauth frames to network: DC:EB:69:BA:79:C9 -- HomeNet
mac_address: 2023-02-26 00:35:56 - Sending 500 deauth frames to network: C4:41:1E:53:7D:8C -- TheCorp
```

## Reading all files in a directory
The line below will read all files in the current directory then output any important data to ~/output.log
```
find . -type f -exec cat {} \; | ds --junk -t -o ~/output.log
```

# Speed Tests
Below is the elapsed time when processing a 5GB test file generated by [ds-test](https://github.com/Drew-Alleman/ds-test). 

When no specific query is provided, ds will search through all possible types of data, which is <b>SIGNIFICANTLY</b> slower than using individual queries.

## Searching all data types

Command         | Speed
----------------|----------------
`cat test.txt \| ds -t -o output.txt` | 00h:03m:35s
`cat test.txt \| ds -t` | 00h:03m:57s
`ds -t -f test.txt` | 00h:04m:16s

## Using specific queries

Command         | Speed
----------------|----------------
`cat test.txt \| ds -t -6` | 00h:02m:10s
`cat test.txt \| ds -t -i -m` | 00h:02m:57s
`cat test.txt \| ds -t -i -m -6` | 00h:02m:56s

# Project Goals
* CSV output
