# Locally hosted Ollama server available to others

As I continue to explore LLMs and their various use cases. Running Ollama server has been my go-to tool to try different LLMs to asses which one is suitable for the application(s), I am working on.

However, with my primary development machine, being a MacBook M1 Pro with 16GB of RAM, it is not the best machine for running some of these models, as response times can take 5 to 15 mins+, depending on the model.

As I have a desktop computer, I use for playing video games, having a GPU is quite important, as it improves performance and response times of the LLMs.

My current PC specs are:

- OS: Windows 10 Pro
- RAM: 16 GB @ 3600 MHz
- GPU: NVIDIA GeFore RTX 4080 with 16 GB RAM.

As I have a GPU on my machine and I run via docker compose, I would test changes on my MacBook and often times, run the latest version of the application again on my desktop. This is inefficient, because I did not know how to open up my machine to be able to connect to the Ollama server running on my Windows machine.

## Cloudflare tunnels

I did not want deal with port-forwarding and/or updating my firewalls, as this activity is not done on a day-to-day basis, so would forget to revert various changes.

> Cloudflare Tunnel provides you with a secure way to connect your resources to Cloudflare without a publicly routable IP address. With Tunnel, you do not send traffic to an external IP — instead, a lightweight daemon in your infrastructure (`cloudflared`) creates [outbound-only connections](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/#outbound-only-connection) to Cloudflare's global network. Cloudflare Tunnel can connect HTTP web servers, [SSH servers](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/use-cases/ssh/), [remote desktops](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/use-cases/rdp/), and other protocols safely to Cloudflare. This way, your origins can serve traffic through Cloudflare without being vulnerable to attacks that bypass Cloudflare [^1].

## Ollama server (docker compose)

If you was not aware, it is possible to run your Ollama server via docker compose and use the host machine GPU.

```yaml
  ollama:  
    image: ollama/ollama:${OLLAMA_TAG:-latest}  
    platform: "linux/amd64"  
    environment:  
      - OLLAMA_KEEP_ALIVE="60m"
      - OLLAMA_HOST=0.0.0.0
    volumes:  
      - ollama:/root/.ollama  
    ports:  
      - "11434:11434"  
    networks:  
      - app  
    restart: always  
#   Enable GPU support using host machine  
    deploy:  
      resources:  
        reservations:  
          devices:  
            - driver: nvidia  
              count: all  
              capabilities: [ gpu ]
```

It is important that we set `OLLAMA_HOST`  to `0.0.0.0` or else it would run on `localhost`. I have [previously run](https://github.com/kwame-mintah/terraform-aws-llm-infrastructure/blob/2a86ed5d81d484f94c17948f929a46d75359a205/scripts/ec2/install_ollama_server.sh#L18) an Ollama server on an EC2 instance. With everything now in place, I run the container and would pull models, text embeddings etc. manually via the `ollama` CLI within the container.

I wanted to use a subdomain, to connect to the Ollama server using the tunnel created.

```markdown
https://something.yourdomain.com  →  Cloudflare  →  Tunnel  →  localhost
```

## Starting the tunnel to Ollama server

I first needed to download the Cloudflare CLI tool (`cloudflared`), at the time of writing I used version `2026.2.0`. The next step depends if you own a domain name or not.

If not, the tunnel created is not suitable for production and subject to change at any given time and is limited.

```bash
cloudflared tunnel --url http://localhost:11434
```

Which would generate a url like this:

```bash
https://random-words.trycloudflare.com
```

If you do have a domain name, you can create the tunnel on a sub-domain. Like I have done.

Authenticate with Cloudflare:

```bash
cloudflared tunnel login
```

Create a tunnel:

```bash
cloudflared tunnel create ollama-tunnel
```

Create a DNS route:

```bash
cloudflared tunnel route dns ollama-tunnel ollama.intuitive-judgments.com
```

Created a configuration file:

```yaml
# path: %USERPROFILE%\.cloudflared\config.yml

tunnel: <TUNNEL-UUID>
credentials-file: C:\Users\<you>\.cloudflared\<TUNNEL-UUID>.json

ingress:
  - hostname: ollama.intuitive-judgments.com
    service: http://localhost:11434
  - service: http_status:404
```

Run the tunnel:

```bash
cloudflared tunnel run ollama-tunnel
```

I then check on a different network, to see if Ollama was reachable, either by visiting `ollama.intuitive-judgments.com` on the browser to get the message `"Ollama is running"`.

Additionally, I can check what models and/or text embeddings were avaliable on Ollama.

```bash
curl https://ollama.intuitive-judgments.com/api/tags
```

## Creating the tunnel via Terraform

I also replicated the tunnel creation via Terraform, using the [cloudflare provider](https://registry.terraform.io/providers/cloudflare/cloudflare/latest) in the following repository [terraform-cloudflare-resources](https://github.com/kwame-mintah/terraform-cloudflare-resources). With this approach, the user will need to run the following command on the chosen machine:

```bash
cloudflared.exe service install eyJhIj...
```

This method replaces both the `config.yaml` and credentials file, that would have been stored / required, when running `cloudflare tunnel run ...` as such the ingress config etc, [is stored in Cloudflare](https://github.com/kwame-mintah/terraform-cloudflare-resources/blob/0e43eeb5729dd8a0af7e6c4ffdb725c0228d63ff/main.tf#L5-L33). This means no configuration or credentials are stored on the machine.

## Next steps

- [ ] Lockdown the tunnel
  - [ ] Create a self-hosted app in Cloudflare Zero Trust auth
  - [ ] Attach a policy for emails I trust and/or GitHub / Google logins etc.
- [ ] Specify an IP allowlist
- [ ] Protect the Ollama API endpoint path `/api`

[^1]: [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/) by Cloudflare

---
#llms #self-hosting #cloudflare #tunnelling
