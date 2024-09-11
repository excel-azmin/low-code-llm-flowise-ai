# low-code-llm-flowise-ai

* Create Network

```
"docker network create \
--driver overlay \
--subnet=10.0.9.0/24 \
--attachable \
flowise-network"
```

* Run the stack  

```
version: "3.7"

services:
    flowise:
        image: elestio/flowiseai:${SOFTWARE_VERSION_TAG}
        environment:
            - PORT=3000
            - FLOWISE_USERNAME=${USERNAME}
            - FLOWISE_PASSWORD=${ADMIN_PASSWORD}
        deploy:
          restart_policy:
            condition: on-failure  
          labels:
            traefik.docker.network: traefik-public
            traefik.enable: "true"
            traefik.constraint-label: traefik-public
            traefik.http.routers.flowiseai--http.rule: Host(${SITES:?No sites set})
            traefik.http.routers.flowiseai--http.entrypoints: http
            traefik.http.routers.flowiseai--http.middlewares: https-redirect
            traefik.http.routers.flowiseai--https.rule: Host(${SITES})
            traefik.http.routers.flowiseai--https.entrypoints: https
            traefik.http.routers.flowiseai--https.tls: "true"
            traefik.http.routers.flowiseai--https.tls.certresolver: le
            traefik.http.services.flowiseai-.loadbalancer.server.port: "3000"    
        networks:
           - traefik-public  
           - flowise-network
        volumes:
            - "flowise:/root/.flowise"

volumes:
  flowise:

networks:
  traefik-public:
    name: traefik-public
    external: true   
  flowise-network:
    name: flowise-network
    external: true
    driver: overlay
    ipam:
      config:
        - subnet: 10.0.9.0/24
```


![image](https://github.com/user-attachments/assets/2a1657f2-5715-426c-ab41-05b8b9fd8d7a)
