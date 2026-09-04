# READMEs Docker
READMEs sobre o trabalho de Redes.
## Objetivo

Nesta primeira etapa, vamos criar uma aplicação web simples utilizando **Python, Flask e Docker**.

A aplicação será executada dentro de um container Docker e poderá ser acessada pelo navegador.

Nesta etapa ainda não utilizaremos banco de dados MySQL nem o servidor Apache. Esses componentes serão adicionados progressivamente nas próximas etapas.

A arquitetura atual é:

```text
Navegador
    │
    │ http://localhost:5000
    ▼
┌─────────────────────────┐
│ Container Docker        │
│                         │
│ Python 3.14-slim        │
│ Flask                   │
│                         │
│ Porta 5000              │
└─────────────────────────┘
```

---

# Estrutura do projeto

Crie uma pasta para o projeto:

```text
projeto-flask/
├── app/
│   ├── app.py
│   └── requirements.txt
└── Dockerfile
```

Os arquivos possuem as seguintes funções:

| Arquivo            | Função                                          |
| ------------------ | ----------------------------------------------- |
| `app.py`           | Código da aplicação Flask                       |
| `requirements.txt` | Dependências Python                             |
| `Dockerfile`       | Instruções para construir a imagem da aplicação |

---

# Passo 1 — Criar a aplicação Flask

Use o comando ```mkdir``` para criar os diretórios/pastas e o comando ```touch``` para criar os arquivos.

Exemplos:
```bash
mkdir projeto-flask
mkdir projeto-flask/app
```

```bash
touch projeto-flask/Dockerfile
touch projeto-flask/app/app.py
touch projeto-flask/app/requirements.txt
```

Para visualizar a árvore:
```bash
tree projeto-flask
``` 

Conteúdo do app.py (***use o editor de linha de comando nano ou vim para editar os arquivos criados***):

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def home():
    return """
    <h1>Minha primeira aplicação Flask</h1>
    <p>Aplicação executando dentro de um container Docker!</p>
    """


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

A rota:

```python
@app.route("/")
```

define o endereço principal da aplicação.

O parâmetro:

```python
host="0.0.0.0"
```

permite que a aplicação receba conexões vindas de fora do container.

A aplicação será executada na porta:

```text
5000
```

---

# Passo 2 — Criar o requirements.txt

Crie:

```text
app/requirements.txt
```

com:

```text
Flask==3.1.2
```

O arquivo `requirements.txt` informa ao `pip` quais bibliotecas Python devem ser instaladas.

Nesta primeira etapa, precisamos somente do Flask.


---

# Passo 3 — Criar o Dockerfile

Na raiz do projeto, crie:

```text
Dockerfile
```

Conteúdo:

```dockerfile
FROM python:3.14-slim

WORKDIR /app

COPY app/requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY app/ .

EXPOSE 5000

CMD ["python", "app.py"]
```

## Principais instruções

### FROM

```dockerfile
FROM python:3.14-slim
```

Define a imagem utilizada como base.

Nesse projeto utilizamos uma imagem do Python 3.14 baseada na variante `slim`.

### WORKDIR

```dockerfile
WORKDIR /app
```

Define `/app` como diretório de trabalho dentro do container.

### COPY

```dockerfile
COPY app/requirements.txt .
```

Copia o arquivo `requirements.txt` para dentro da imagem.

Depois:

```dockerfile
COPY app/ .
```

copia o código da aplicação.

### RUN

```dockerfile
RUN pip install --no-cache-dir -r requirements.txt
```

Instala as dependências Python.

### EXPOSE

```dockerfile
EXPOSE 5000
```

Indica que a aplicação utiliza a porta 5000 dentro do container.

### CMD

```dockerfile
CMD ["python", "app.py"]
```

Define o comando executado quando o container for iniciado.

---

# Passo 4 — Baixar a imagem do Python

Antes de construir nossa imagem, podemos baixar a imagem base:

```bash
docker pull python:3.14-slim
```

O comando `docker pull` baixa uma imagem de um registro de imagens, como o Docker Hub.

Verifique as imagens disponíveis:

```bash
docker images
```

ou:

```bash
docker image ls
```

A imagem `python:3.14-slim` deverá aparecer na lista.

---

# Passo 5 — Construir a imagem da aplicação

Entre na pasta raiz do projeto:

```bash
cd projeto-flask
```

Execute:

```bash
docker build -t minha-flask .
```

O parâmetro:

```text
-t minha-flask
```

define o nome da imagem.

O ponto:

```text
.
```

indica que o Docker deve utilizar o diretório atual como contexto para o build.

Depois podemos verificar:

```bash
docker images
```

A imagem criada deverá aparecer como:

```text
minha-flask
```

---

# Passo 6 — Criar e executar o container

Execute:

```bash
docker run -d -p 5000:5000 --name meu-flask minha-flask
```

Esse comando cria um container a partir da imagem `minha-flask`.

## Entendendo o comando

```text
docker run
```

Cria e executa um container.

```text
-d
```

Executa o container em segundo plano (*detached mode*).

```text
-p 5000:5000
```

Mapeia a porta do computador para a porta do container:

```text
Computador:5000 → Container:5000
```

```text
--name meu-flask
```

Define o nome do container.

```text
minha-flask
```

É a imagem utilizada para criar o container.

---

# Passo 7 — Testar no navegador

Verifique se o container está executando:

```bash
docker ps
```

Deverá aparecer o container:

```text
meu-flask
```

Agora abra o navegador e acesse:

```text
http://localhost:5000
```

A aplicação deverá apresentar:

```text
Minha primeira aplicação Flask

Aplicação executando dentro de um container Docker!
```

Neste momento temos nossa primeira aplicação web executando dentro de um container Docker.

---

# Arquitetura atual

Ao final desta etapa, temos:

```text
                    Navegador
                        │
                        │ HTTP
                        │
                        ▼
                localhost:5000
                        │
                        ▼
           ┌──────────────────────────┐
           │      Container Docker    │
           │                          │
           │      Python 3.14-slim    │
           │             +            │
           │           Flask          │
           │                          │
           │         :5000            │
           └──────────────────────────┘
```

# Experiências
| Membro | Nome | Opinião |
| ------ | ---- | ------- |
| ![Foto de Gabriel Osti](https://suap.ifsp.edu.br/media/alunos/fotos/2024/5gjJrkhG2px6iDWN3nhevac-VDNPS3SMB7bfJ1oGRas.jpg) | Gabriel Osti | _"O trabalho sobre o Docker foi, para mim, muito interessante, pois me mostrou uma tecnologia que não conhecia e me instigou a pesquisar mais sobre ela. Observar seu processo de download e execução foi igualmente interessante. Este foi, deveras, um trabalho em grupo que terá efeitos mesmo após o seu término."_ |
| ![Foto de Igor Arantes](https://suap.ifsp.edu.br/media/alunos/fotos/2024/eYPTw8P7wiLg8IdSuvYXFY7AugBXaAEcqYh0GJlIeUQ.jpg) | Igor Arantes | _"Realizar este projeto foi uma experiência muito interessante, principalmente por me permitir conhecer melhor o Docker, uma tecnologia que eu ainda não conhecia. Durante a atividade, aprendi sobre containers, imagens e alguns comandos do Docker, além de participar do trabalho em grupo. No final, acredito que essa atividade ajudou bastante a aumentar meus conhecimentos sobre tecnologia."_ |
| ![Foto de Mauro Lopes de Almeida Junior](https://suap.ifsp.edu.br/media/alunos/fotos/2024/TdtMTqav68iQa7LwQc_ETaWuQY6ZIVUQM_ZQ32PtbhE.jpg) | Mauro Lopes de Almeida Junior | _"achei épico."_ |
| ![Foto de Miguel Obara](https://suap.ifsp.edu.br/media/alunos/fotos/2024/4yFtdVJ2Y08TzuwN1bEBURvLgsvTqduQvg9guQjM4Sw.jpg) | Miguel Obara | _"O trabalho foi bastante divertido! Foi muito legal aprender sobre virtualização e Docker, que são tecnologias inovadoras e quebram um galho GIGANTE no desenvolvimento!"_ |
| ![Foto de Rafael Francisco de Lima da Silva](https://suap.ifsp.edu.br/media/alunos/fotos/2024/V4QuIRE1GfgD5I92eXvdl0tiOLqk7IyWsK4mSxN3Kls.jpg) | Rafael Francisco de Lima da Silva | _"Eu gostei muito do trabalho. Foi extremamente gratificante saber da existência desse tipo de programa, que pode economizar tempo e espaço enquanto proporciona um ambiente de desenvolvimento leve e limpo. "_ |
| ![Foto de Homem](https://github.com/Mauro5049/readmes-docker/blob/main/homem.png) | Custonho Orabó | _"Se pessoas boas podem se tornar cruéis após sofrerem o suficiente, e pessoas más podem se tornar boas depois de serem amadas o suficiente, então o que verdadeiramente separa o bem do mal?"_ |
