# HAProxy Load Balancer Web Server Lab

## Project Description

This project demonstrates a basic HAProxy load balancer configuration using two Apache HTTP web servers.

The setup contains:

* Web Server 1
* Web Server 2
* HAProxy load balancer
* Apache HTTP Server
* Firewalld HTTP configuration
* HAProxy frontend and backend configuration
* Round-robin load balancing

## Lab Environment

| Server       | IP Address      | Service     |
| ------------ | --------------- | ----------- |
| Web Server 1 | `192.168.0.105` | Apache HTTP |
| Web Server 2 | `192.168.0.107` | Apache HTTP |
| HAProxy      | `192.168.0.102` | HAProxy     |

## Project Architecture

```text
                    Client
                       |
                       |
                192.168.0.102:80
                       |
                       v
                +-------------+
                |   HAProxy   |
                |  HTTP Front |
                +-------------+
                       |
              Round-Robin Balance
                 /             \
                /               \
               v                 v
      192.168.0.105:80   192.168.0.107:80
        Web Server 1        Web Server 2
          Apache              Apache
```

## Web Server 1 Setup

Apache HTTP Server was installed and started on Web Server 1.

```bash
yum install httpd-* -y

systemctl start httpd
systemctl enable httpd

cd /var/www/html/

touch index.html

vim index.html
```

The web page displays:

```text
This is Web Server 1
```

## Web Server 2 Setup

Apache HTTP Server was installed and started on Web Server 2.

```bash
yum install httpd-* -y

systemctl start httpd
systemctl enable httpd

cd /var/www/html/

touch index.html

rm -rf Apache.com/

vim index.html
```

The web page displays:

```text
This is Web Server 2
```

## Firewalld Configuration

HTTP service was allowed through firewalld on Web Server 1.

```bash
firewall-cmd --permanent --add-service=http

firewall-cmd --reload

firewall-cmd --list-all
```

The HTTP service appears in the active firewalld services.

## HAProxy Installation

The EPEL repository was installed:

```bash
yum install epel-release -y
```

HAProxy was then installed:

```bash
yum install haproxy -y
```

The HAProxy configuration directory contains:

```text
/etc/haproxy/
└── haproxy.cfg
```

## HAProxy Configuration

The HAProxy frontend listens on port `80`:

```text
frontend http_front
    bind *:80
    default_backend http_back
```

The backend uses round-robin load balancing:

```text
backend http_back
    balance roundrobin
    server  webserver1  192.168.0.105:80 check
    server  webserver2  192.168.0.107:80 check
```

## Complete Backend Configuration

```text
backend app
    balance     roundrobin
    server      app1 127.0.0.1:5001 check
    server      app2 127.0.0.1:5002 check
    server      app3 127.0.0.1:5003 check
    server      app4 127.0.0.1:5004 check

backend http_back
    balance roundrobin
    server  webserver1  192.168.0.105:80 check
    server  webserver2  192.168.0.107:80 check
```

## Verification

The two Apache web servers were first accessed directly:

```text
192.168.0.105
```

Result:

```text
This is Web Server 1
```

and:

```text
192.168.0.107
```

Result:

```text
This is Web Server 2
```

After configuring HAProxy, the load balancer was accessed using:

```text
192.168.0.102
```

The browser displayed the web server responses through the HAProxy load balancer.

## Technologies Used

* CentOS 8
* Apache HTTP Server
* HAProxy
* Firewalld
* HTTP
* Round-robin load balancing

## Screenshots

### Web Server 1 Setup

![Web Server 1 Setup](screenshots/01_webserver1_setup.png)

### Web Server 2 Setup

![Web Server 2 Setup](screenshots/02_webserver2_setup.png)

### Firewalld Configuration

![Firewalld Configuration](screenshots/03_firewall_configuration.png)

### Direct Web Server Access

![Direct Web Server Access](screenshots/04_webservers_direct_access_verification.png)

### HAProxy EPEL Repository Installation

![HAProxy EPEL Repository](screenshots/05_haproxy_epel_repo_installation.png)

### HAProxy Package Installation

![HAProxy Installation](screenshots/06_haproxy_package_installation.png)

### HAProxy Configuration Directory

![HAProxy Configuration Directory](screenshots/07_haproxy_configuration_directory.png)

### HAProxy Frontend Configuration

![HAProxy Frontend](screenshots/08_haproxy_frontend_backend_config_part1.png)

### HAProxy Backend Configuration

![HAProxy Backend](screenshots/09_haproxy_frontend_backend_config_part2.png)

### HAProxy Load Balancer Verification

![HAProxy Verification](screenshots/12_haproxy_load_balancer_verification.png)
