# Install

# Install Docker Engine for Ubuntu

1. Make bash file to run with content inside:

```jsx
# 

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

```

1. Change mode with execute permission

```jsx
chmod +x file_name.sh
```

1. Run bash file

```jsx
sudo ./file_name.sh
```

1. Install docker component from apt

```jsx
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

1. Test

```jsx
docker run -d -p 8080:80 docker/welcome-to-docker

##open brower and navigate to ip_address:8080 and successful!
```