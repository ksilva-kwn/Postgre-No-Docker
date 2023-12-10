
<img width=100% height=450px src="https://raw.githubusercontent.com/ksilva-kwn/icones-pro-perfil/main/1__8wZWFP92axdesNPKbZOUQ.webp?token=GHSAT0AAAAAACLFTWHCDXTDNMI6OIY5FFPOZLV6AUQ" width="600" height="300">
<br>

#### Geralmente, quando estamos começando uma aplicação, ou quando queremos rodar a aplicação de alguém localmente, precisamos configurar o banco de dados, sendo necessário instalar ele na máquina. Há também casos em que já temos algum banco rodando localmente e precisamos criar um novo, isso acaba dificultando a configuração.
####  E por falar em configuração, oque acontece quando fazemos muitas configurações em nosso banco local e depois temos que rodar ele em outra máquina? Temos que configurar tudo novamente?
####  Felizmente, com o surgimento dos containers, isso se torna muito mais fácil, uma vez que nosso banco de dados se resume a uma imagem, que roda em um container juntamente com todas as configurações e dependências. Isso permite rodar nosso banco em diferentes ambientes, sem ter que se preocupar com configuração, compatibilidade, etc.
####  Neste exemplo, irei mostrar como rodar um banco de dados Postgres utilizando o Docker, no final, mostrarei como mapear um volume para que nossos dados fiquem gravados na máquina.

<br>

# Primeiramente, para que possamos iniciar, é necessário instalar o Docker na sua máquina. Caso ainda não tenha ele instalado, vá neste link e veja como prosseguir:
```
https://docs.docker.com/get-docker/
```
Feito isso, vamos baixar a imagem do Postgres, para isto, abra o terminal e execute o seguinte comando. 
```
docker pull postgres
```

Em seguida, verifique conferir se o download da imagem foi efetuado com sucesso.

```
docker image ls
```

# Se tudo estiver certo, podemos executar nosso container.

#### Por padrão, o Postgres roda na porta 5432, mas isso fica disponível apenas dentro do container, para permitir que ele possa ser acessado pela máquina local, precisamos um mapping da porta interna, para porta externa. Fazemos isso com o parametro -p, logo após, indicamos uma porta local e para qual porta deve redirecionar, por exemplo, -p 5432:5432.

#### Para definirmos qual a senha do banco, devemos alterar uma variável de ambiente, -e POSTGRES_PASSWORD=suasenha. Isso é o suficiente para podermos rodar nosso banco, agora, iremos subir o container com o comando run, passando as configurações que vimos acima.

```
docker run -p 5432:5432 -e POSTGRES_PASSWORD=SuaSenha postgres
```

# Ao fazer isso, temos nosso container rodando. Ele estará disponível na porta 5432, o usuário padrão será postgres e a senha.

Executando o comando visto acima, temos nosso banco rodando, o problema é que por padrão, todos os dados são armazenados internamente dentro do container, e quando paramos ele, perdemos tudo. Para que possamos gravar os dados permanente, utilizaremos os volumes do docker, iremos definir uma pasta da máquina que será responsável pelo armazenamento, no meu caso, criei uma pasta com o nome database no diretório “/tmp/database”. Feito isto, vamos mapear a pasta de armazenamento dentro do container, para nossa pasta que criamos.

```
docker run -p 5432:5432 -v /tmp/database:/var/lib/postgresql/data -e POSTGRES_PASSWORD=SuaSenha postgres
```

### Agora para conferir se o container está em execução.

```
docker ps
```

# E como acessar esse banco por algum SGBD (Sistema Gerenciador de Banco de dados)? 

Primeiramente você precisará de um client PostgreSQL para se conectar ao banco de dados, pode ser o psql ou uma ferramenta gráfica como o DBeaver. No meu caso estarei utilizando o SQLGate for Postgre.

Você precisará substituir as variáveis de ambiente no client.

Nome da Conexão: postgres. <br>
Tipo de Banco de Dados: PostgreSQL (isso caso você estiver utilizando outro client). <br>
Host: localhost ou o endereço IP do seu Docker, se necessário. <br>
Porta: 5432 (ou a porta que você mapeou durante a execução do contêiner). <br>
Usuário: postgres (ou o nome do usuário se você tiver configurado). <br>
Senha: A senha que você definiu ao criar o contêiner. <br>
