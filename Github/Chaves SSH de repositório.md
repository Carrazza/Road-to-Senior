
### 5.3 Se conectando com o GitHub com SSH

O protocolo **Secure Shell** (SSH) é uma forma de nos conectarmos a um servidor remoto de modo seguro, usando criptografia. A autenticação da conexão é feita através de um par de chaves (ou senhas), onde uma fica armazenada na máquina do cliente que está buscando a conexão, e a outra no servidor a ser contactado. A vantagem de usar esse sistema é que não precisamos inserir o nosso login e senha sempre que quisermos nos conectar ao GitHub.

Estou assumindo uma instalação zerada do WSL, e portanto que nenhuma chave SSH esteja presente. Caso esse não seja o seu caso, verifique se você já possui uma chave seguindo [essas instruções](https://docs.github.com/pt/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys). Se sim, você poderá usá-la ou gerar uma nova seguindo os passos abaixo.

#### Passo 1: Gerando uma nova chave SSH

Execute o comando abaixo no terminal do Ubuntu, substituindo o email de exemplo pelo que você usa na sua conta do GitHub. Ele irá gerar uma nova chave SSH associada ao email passado.

```bash
ssh-keygen -t ed25519 -C "seu_email@exemplo.com"
```

Será retornada uma mensagem avisando que a chave está sendo gerada, e uma outra solicitando um caminho e nome para o arquivo da mesma. Basta apertar Enter para aceitar a localização padrão.

```bash
Generating public/private ed25519 key pair.
Enter a file in which to save the key (/home/usuario/.ssh/id_ed25519):
```

Em seguida, será solicitada uma frase secreta que serve como uma camada de proteção adicional. Você pode ignorar a criação dela apertando Enter duas vezes. Se quiser adicionar uma mais tarde, siga [essas instruções](https://docs.github.com/pt/authentication/connecting-to-github-with-ssh/working-with-ssh-key-passphrases#adding-or-changing-a-passphrase).

```bash
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

Pronto! Par de chaves gerado!

#### Passo 2: Adicionando a chave ao ssh-agent

O ssh-agent é o programa que irá gerenciar a nossa chave SSH. Com o comando abaixo nós iremos inicializá-lo e deixá-lo pronto para que o terminal possa utilizá-lo para conectar-se ao servidor do GitHub. A mensagem `Agent pid XXXXX` será exibida indicando que o programa foi iniciado com sucesso, sendo que o número representado por `XXXXX` é um identificador único do mesmo.

```bash
eval "$(ssh-agent -s)"
```

Em seguida, execute o comando abaixo para adicionar a chave ao ssh-agent (Obs.: caso a chave tenha sido criada com um nome diferente do padrão, é necessário substituir `id_ed25519` por ele):

```bash
ssh-add ~/.ssh/id_ed25519
```

Será retornada a mensagem `Identity added: /home/usuario/.ssh/id_ed25519 (seu_email@exemplo.com)`.

#### Passo 4: Adicionando chave SSH ao GitHub

Primeiro, execute o comando `cat ~/.ssh/id_ed25519.pub` a fim de exibir a sua chave no terminal. Depois selecione ela, e copie para a área de transferência.

Em seguida, entre em sua conta do GitHub, e clique na sua foto de perfil no ícone superior direito. No menu aberto clique em `Settings`.

Na seção "Access" da barra lateral esquerda, clique em `SSH and GPG keys`. Em seguida clique no botão `New SSH key` ou `Add SSH key`. Então, no campo "Title" basta adicionar um título para a sua chave (pode ser uma referência ao computador do qual você está acessando), e no campo "Key" colar o conteúdo da chave que foi copiado do terminal. No campo "Key Type" você pode deixar selecionada a opção "Authentication Key". Por fim, clique em `Add SSH key`, e confirme o seu login, caso seja solicitado.

Com isso, você pode agora clonar o seu projeto para a sua máquina local (no WSL) usando o protocolo SSH, através do comando `git clone`.

![git clone ssh](https://i.imgur.com/PgGp6k3.png)

Tomando como exemplo o endereço SSH do repositório na imagem acima, o comando a ser executado seria:

```sh
git clone git@github.com:andrei-alves/meu-projeto.git
```

Mas atenção: Na sua primeira conexão, será exibida uma mensagem como a abaixo:

```bash
The authenticity of host 'github.com (IP ADDRESS)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
Are you sure you want to continue connecting (yes/no)?
```

Isso acontece porque o GitHub ainda não é um host conhecido pela nossa máquina. E para garantir a autenticidade da chave retornada na mensagem, você pode comparar com a que é disponibilizada na [página oficial](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints) (compare com a do tipo Ed25519). Você pode usar [essa ferramenta](https://www.diffchecker.com/) para comparar as duas chaves, e ao copiar a que é retornada no terminal não inclua o ponto final (por exemplo, na messagem acima você copiaria `SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU`).

Ao confirmar a autenticidade, digite `yes` no terminal e aperte Enter. E pronto! Agora você pode fazer os seus commits localmente e enviá-los para o seu repositório remoto com facilidade.

![git clone](https://i.imgur.com/MNjpCRL.png)