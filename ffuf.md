# FFuf

Tool for fuzzing websites.


### Installation
```shell-session
apt install ffuf -y
```

### Important options

#### -w
Wordlist file path and (optional) keyword separated by colon. 

eg. '/path/to/wordlist:KEYWORD'


#### -u

Target URL

We can assign a keyword to a wordlist to refer to it where we want to fuzz. For example, we can pick our wordlist and assign the keyword FUZZ to it by adding :FUZZ after it.
Next, as we want to be fuzzing for web directories, we can place the FUZZ keyword where the directory would be within our URL, with:

```shell-session
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ
```

We can use these options to fuzz file extensions.
```
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ
```
The wordlist we chose already contains a dot (.), so we will not have to add the dot after "index" in our fuzzing.

#### -recursion

Scan recursively. Only FUZZ keyword is supported, and URL (-u) has to end in it. (default: false)

#### -recursion-depth
Maximum recursion depth. (default: 0)

#### -e
Comma separated list of extensions. Extends FUZZ keyword.
  
  
#### -v
Verbose output, printing full URL and redirect location (if any) with the results. (default: false)

```
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v
```

We can use these options to fuzz subdomains.

#### -H                 
Header `"Name: Value"`, separated by colon. Multiple -H flags are accepted.

-H can be used for VHost fuzzing.
```
ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'

```

#### -fs             
Filter HTTP response size. Comma separated list of sizes and ranges

#### -X
HTTP method to use
default GET

#### -D
POST data

Fuzzing POST parameter:
```
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx
```

sudo sh -c 'echo "SERVER_IP  academy.htb" >> /etc/hosts'

for i in $(seq 1 1000); do echo $i >> ids.txt; done