<h1 align="center"> Inventário Auto AWS </h1>

**Este projeto tem como objetivo a criação de uma função lambda que retorna um inventário de recursos da Amazon Web Services (AWS)**
**Os recursos mapeados por essa automação são: EC2, EKS, EBS, EFS, FSX, S3, DynamoDB, DocumentDB, RDS e ApiGateWay**

## Início

**Primeiro clone o repositório**

- git clone https://github.com/joaolfms/inventario-auto.git

**O script foi escrito em Python 3 e necessita das seguintes bibliotecas: boto3, json, pandas, datetime e botocore**

**Para isso instale os requirements em uma pasta vazia chamada python**

- mkdir python
- cd python
- pip install -r requirements.txt -t .

**Depois dos requirements instalados na pasta python comprima a pasta python**

- zip -r python.zip python

## Criando a role para a função lambda

**Na conta principal em que a função lambda funcionará vá até o console da AWS e vá até o painel do IAM**

- Vá em roles
- Create role
- Em trusted entity selecione a opção AWS service
- Em Common use cases marque a opção lambda depois clique em next
- Agora clique em create policy
- Em policy editor selecione a opção json
- Copie o conteudo do arquivo inventario.json e cole no editor (Não esqueça de trocar o id das contas e o nome do bucket para os seus)
- Defina o nome da policy 'inventario_role', decrição e tags são opcionais
- Attach a policy criada a role

**A função lambda usa o STS do boto3 para assumir a role da função e executar o script**

- Na role vá ate Trust relationships da role 'inventario_role'
- Em edit trust policy adicione o conteudo do aquivo Trust.json depois em update policy
**Obs.: Essa Trust relationships Policy deve conter o ARN da própria role ('inventario_role')**

## Cross Account

**para fazer com que a função itere também sobre outras contas dentro de uma organization é necessário seguir os passos seguintes**

- Copie a policy da role criada anteriormente 
- crie a role na conta que deseja fazer o Cross Account e cole a policy (Precisa ser igual as roles) e dê o nome 'inventario_cross'
- Em Trust relationships vá em edit trust policy o cole o conteudo de trust.json (Nesse caso o ARN precisa ser igual ao da role da conta principal 'inventario_role')
- Na role 'inventario_role' na conta principal, vá em add permissions e clique em Create inline policy
- copie o conteudo de cross-acount.json e cole na inline policy (Note que o ARN deve ser igual ao da role 'inventario_cross')
- repita os passos anterior para adicionar o cross account em outras contas (Preste atenção nos ARNs para não confundir)

## Criando a função lambda

**No console AWS da conta principal vá até lambda**

- Create function
- Escolha o nome da função
- Em runtime escolha Python 3.9
- Em Architeture mantenha em x86_64
- Em Permissions clique em Change default execution role
- Use an existing role
- Escolha a role 'inventario_role'
- Create function

**Abra a função criada e copie o codigo de inventario-auto e cole na função**

- No painel do lambda vá até layers
- Clique em Create layer
- Dê um nome a Layer, descrição é opcional
- Faça Upload do arquivo python.zip criado no inicio
- Architeture x86_64
- Runtimes python 3.9
- Create

**Na função lambda criada role até o final da pagina**

- Clique em add layers na opção layers
- Deixe a opção AWS layers marcada
- Escolha a Layer AWSSDKPandas-Python3.9
- Em version escolha a ultima versão
- depois clique em add
- Role para baixo novamente e adicione outra layer
- Agora seleciona a opção Custom layer
- Selecione a Layer que criamos e a versão
- Clique em add

**Pronto agora seu inventário auto está implementado, não esqueça de editar os ids das contas na linha 11 do codigo**


## Desenvolvedor

Este projeto foi desenvolvido por **João Lucas Férras da Motta dos Santos**.

## Contribuição

Este projeto é de código aberto e contribuições são bem-vindas. Para contribuir, siga os seguintes passos:

- Fork o repositório do projeto.
- Crie uma branch para sua nova feature ou correção de bug: git checkout -b nome-da-feature-ou-bugfix
- Faça as mudanças necessárias e faça commit das mudanças: git commit -am 'descrição do commit'
- Push para a branch: git push origin nome-da-feature-ou-bugfix
- Crie um Pull Request para o repositório original.
- Aguarde a revisão do seu Pull Request.
