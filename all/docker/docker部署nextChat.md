docker-compose.yml

```yml
services:
  nextChat: 
    image: yidadaa/chatgpt-next-web:latest
    container_name: nextChat
    restart: always
    environment:
    ## 必填参数实际没有使用的话没啥用
    ##  - OPENAI_API_KEY=123
    ## 客户端密码
      - CODE=qq532955362
      - BASE_URL=https://api.deepseek.com/v1
      - DEEPSEEK_URL=https://api.deepseek.com
      - CUSTOM_MODELS=-all,+deepseek-chat,+deepseek-reasoner
      - DEEPSEEK_API_KEY=sk-xxxx
      - HIDE_USER_API_KEY=1
    ## 用户自定义设置隐藏   
    ##  - ENABLE_BALANCE_QUERY=1
      - DEFAULT_MODEL=deepseek-chat
    networks:
      - nginx-network
networks:
  ## 定义的网络的名字
  nginx-network:
    name: nginx-network
    ## 如果外部已经创建了则使用这个
    external: true

```