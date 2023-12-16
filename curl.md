#### Verbose

Get more info (about headers and stuff) using -v or --verbose

#### Basic Auth

curl --user daniel:secret http://example.com/

#### Simple POST

curl -X POST -d 'name=admin&shoesize=12' http://example.com/

Works with JSON data too.

```shell-session
curl -X POST -d '{"search":"london"}' -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' -H 'Content-Type: application/json' http://<SERVER_IP>:<PORT>/search.php
```

#### Skip the certificate check

Use -k flag

#### See only response Headers

Use the `-I` flag to send a `HEAD` request and only display the response headers.

#### See both the headers and the response body

Use the -i flag.

#### Set headers

Use the -H flag.

```shell-session
curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<SERVER_IP>:<PORT>/
```

#### Follow redirect

Use -L flag

#### Set Cookie

Use -b flag

```shell-session
curl -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/
```

#### Format JSON response

Use | jq

```shell-session
curl http://<SERVER_IP>:<PORT>/api.php/city/london | jq
```

#### Silence

The opposite of verbose is, of course, to make curl more silent. With the `-s` (or `--silent`) option you make curl switch off the progress meter and not output any error messages for when errors occur. It gets mute. It will still output the downloaded data you ask it to.

With silence activated, you can ask for it to still output the error message on failures by adding `-S` or `--show-error`.
