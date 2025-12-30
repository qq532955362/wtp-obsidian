docker-compose.yml

```yml
version: "3.9"
services:
  nextChat: 
    image: yidadaa/chatgpt-next-web:latest
    container_name: nextChat
    restart: always
    environment:
    ## 必填参数实际没有使用的话没啥用
      - OPENAI_API_KEY=123
    ## 客户端密码
      - CODE=qq532955362
      - BASE_URL=https://api.deepseek.com
      - DEEPSEEK_URL=https://api.deepseek.com
      - CUSTOM_MODELS=-all,+deepseek-chat,+deepseek-reasoner
      - DEEPSEEK_API_KEY=sk-116867d151e34642bd649ffca201186f
      - HIDE_USER_API_KEY=1
      - ENABLE_BALANCE_QUERY=1
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