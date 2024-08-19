# cloudflare-docker-proxy

![deploy](https://github.com/chinayin/cloudflare-docker-proxy/actions/workflows/deploy.yaml/badge.svg)

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/chinayin/cloudflare-docker-proxy)

> If you're looking for proxy for helm, maybe you can try `cloudflare-helm-proxy`.

## Deploy

1. fork this project
2. modify the link of the above button to your fork url
3. click the button, you will be redirected to the deploy page

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/chinayin/cloudflare-docker-proxy)

## Config tutorial

1. use cloudflare worker host: only support proxy one registry
   ```javascript
   const routes = {
     "${workername}.${username}.workers.dev/": "https://registry-1.docker.io",
   };
   ```
2. use custom domain: support proxy multiple registries route by host
   - host your domain DNS on cloudflare
   - add `A` record of xxx.example.com to `192.0.2.1`
   - deploy this project to cloudflare workers
   - add `xxx.example.com/*` to HTTP routes of workers
   - add more records and modify the config as you need
   ```javascript
   const routes = {
     "docker.mirror.xxxx.cn": "https://registry-1.docker.io",
     "quay.mirror.xxxx.cn": "https://quay.io",
     "gcr.mirror.xxxx.cn": "https://k8s.gcr.io",
     "k8s-gcr.mirror.xxxx.cn": "https://k8s.gcr.io",
     "ghcr.mirror.xxxx.cn": "https://ghcr.io",
   };
   ```

## Usage

1. cd `/etc/containerd`

   ```bash
   ├── cert.d
   │   ├── docker.io
   │   │   └── hosts.toml
   │   ├── gcr.io
   │   │   └── hosts.toml
   │   └── registry.gitlab.com
   │       └── hosts.toml
   └── config.toml
   ```

2. `hosts.toml`

   ```toml
   server = "https:/registry-1.docker.io"

   [host."https://docker.mirror.xxxx.cn"]
     capabilities = ["pull", "resolve"]
   ```

3. restart containerd

   ```bash
   systemctl restart containerd
   ```

4. test
   ```bash
   crictl pull alpine:latest
   crictl pull gcr.io/google-containers/nginx:v1
   crictl pull registry.gitlab.com/gitlab-org/gitlab-runner:alpine
   ```

## Reference

- [Deploy your Worker to Cloudflare](https://developers.cloudflare.com/workers/wrangler/commands/#deploy)
- [System environment variables](https://developers.cloudflare.com/workers/wrangler/system-environment-variables/)
