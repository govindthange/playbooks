# How to delete all containers and images from docker?

If you prefer to remove containers and images manually, you need to ensure that you are working in the correct docker context, then stop and remove all the containers, and finally remove the images.

## Step 1. Switch to correct context.

Ensure that you are using desktop-linux context. This is the context which is linked to docker desktop.

```
govind@thinkpad:~/projects/ai-agents/agentic-email-crew$ docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                                   KUBERNETES ENDPOINT   ORCHESTRATOR
default *           moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                                             
desktop-linux       moby                Docker Desktop                            unix:///home/govind/.docker/desktop/docker.sock                         

govind@thinkpad:~/projects/ai-agents/agentic-email-crew$ docker context use desktop-linux 
desktop-linux
Current context is now "desktop-linux"

govind@thinkpad:~/projects/ai-agents/agentic-email-crew$ docker context list
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                                   KUBERNETES ENDPOINT   ORCHESTRATOR
default             moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                                             
desktop-linux *     moby                Docker Desktop                            unix:///home/govind/.docker/desktop/docker.sock                         


govind@thinkpad:~/projects/ai-agents/agentic-email-crew$ docker context show
desktop-linux
```

## Step 2. Stop all containers.

Stop all running containers first.

> docker stop $(docker ps -aq)

```bash
govind@thinkpad:~/projects/ai-agents/agentic-email-crew$ docker stop $(docker ps -aq)
81cb5b71f270
b8767f4333e9
2abed5a645e2
```

## Step 3. Remove all containers.

Force-remove all containers, even if they are running, with a single command (use with caution, as this sends a SIGKILL signal):

> docker rm -f $(docker ps -aq)

```bash
govind@thinkpad:~/projects/ai-agents/agentic-email-crew$ docker rm -f $(docker ps -aq)
81cb5b71f270
b8767f4333e9
2abed5a645e2
```

## Step 4. List all images.

List all images before removing.

> docker rmi -f $(docker images -aq)

```bash
govind@thinkpad:~/projects/ai-agents/agentic-email-crew$ docker images -aq
7fcd043cbfe6
c4205bdb7913
cdbd336903de
81908ba63c6a
18fe54bb2a24
f2de8ed54c7b
8b912e3381c0
```

## Step 5. Remove all images.

If any images are still referenced (e.g., intermediate layers or multiple tags), you may need to force the removal:

> docker rmi -f $(docker images -aq)

```bash
govind@thinkpad:~/projects/ai-agents/agentic-email-crew$ docker rmi -f $(docker images -aq)
Untagged: agentic-email-crew-crewai-insight-service:latest
Deleted: sha256:7fcd043cbfe60f59f3f6690bd7fcaabce559e561c2fe4aff939955773c06d3c9
Untagged: agentic-email-crew-react-shell:latest
Deleted: sha256:c4205bdb791329ee0aba3ad0dd01ebfea0aef28db57c23c33cd09aabcda4e161
Untagged: agentic-email-crew-email-reader-ui:latest
Deleted: sha256:cdbd336903ded0d7d6fd39aa5af0e906b1aac1003e9cc98e9bce9b917bbd04ef
Untagged: agentic-email-crew-email-reader-service:latest
Deleted: sha256:81908ba63c6a5d18bfeecec49ce29aaebb9942133d4e8139e8b1d2a936abbd77
Untagged: agentic-email-crew-email-reader-console:latest
Deleted: sha256:18fe54bb2a2439ba0e71bfe984b6e66cb13ae0e57dc9e87a07f39b0a772e5839
Untagged: ollama/ollama:latest
Untagged: ollama/ollama@sha256:0ff452f6a4c3c5bb4ab063a1db190b261d5834741a519189ed5301d50e4434d1
Deleted: sha256:f2de8ed54c7b02ee05e42abaa454978c76fb552a883877b37588ebb62f31e41a
Deleted: sha256:c233e1329c8f28ea802a90a8ed6449b7023ec5c9bb6c09ffe2c9c57eaae6ff62
Deleted: sha256:8a1d03619d2115db55c509f8eb31d64b720258f2cc3247ea3daa84878474ce60
Deleted: sha256:26856ec1115c3e85d815917fa3a6d0abf899e9871b350f2f17168413152fcb45
Deleted: sha256:efafae78d70c98626c521c246827389128e7d7ea442db31bc433934647f0c791
Untagged: jlesage/thunderbird:latest
Untagged: jlesage/thunderbird@sha256:8f9fa477b3afbd992393d34229ccffe4de4bd10f5ed2f7317fe1e08301164767
Deleted: sha256:8b912e3381c06dec3f7837667049df3ca7913a52cd3150acc27d6f0162747680
Deleted: sha256:825f6a7c12852a0bc24eff9f5a49af507fe20713f28609ec41a9a71987bdcd7b
Deleted: sha256:da9ebce37bd265e577ce5f4d43093c0bee2f0695c3b97006c513d03cfcd256bf
Deleted: sha256:89daab857d24620bd2030f7bb364174432df1bfca00c23f7118c90103843331e
Deleted: sha256:150615c08636ab0c8a79f91c3b81f09fd2ec5e19b38c5497aaa56910ce5eb391
Deleted: sha256:98f67958438e2895620052e021f2ff580299d84f276dbe0a639c5ae37e235cf9
Deleted: sha256:647113b63daacdbf0f8328c27b513a97ba62759f72bad3fb2c9bb9d7e45e783d
Deleted: sha256:a66af86ee7d99be1fdffef4a44cbf39f272191988a19a9e43dd82e8f3c744414
Deleted: sha256:1ecbff5c0a34504c616bf20d19e6359fa5071a65410fde4e695df59e82ec45cd
Deleted: sha256:37621182fd9415353231a3cc1b87cc349b5e53c396a9184983363f11e39513c2
Deleted: sha256:8f34993307fae1e153d7460bea53e5717e3592cf1e94fae99233a42cd053ee1d
Deleted: sha256:9f45746ceea4d79f52ad11dca6cfb01ec9b8ad86dce61346852718ec0b508e3c
Deleted: sha256:fdbde7008c06dc76f8c8947f54a46d0cac3fd144a3a64bb8a58c5910fd2d64f5
Deleted: sha256:5d40bf24163d1614ec25fabc00577549d4754e596bdd2c1944aca7861208218a
Deleted: sha256:2853ccb933b26bcaac91c9c817738bde18f4916bf43480f28ec09a93ed714677
Deleted: sha256:82a6353b95c150bb73f88040a0b466095507bbb0570a02348e7c6c3154ce3205
Deleted: sha256:7c4d97b5c81be6eaa124182028946a98151e10dea605f85b583d2b19a3cd10ae
Deleted: sha256:418dccb7d85a63a6aa574439840f7a6fa6fd2321b3e2394568a317735e867d35
```
