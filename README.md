# Integração - BrowserStack - Gradle

<img src="https://img.shields.io/github/last-commit/gabriel-secchi/TesteBrowserStack" />
<img src="https://img.shields.io/snyk/vulnerabilities/github/gabriel-secchi/TesteBrowserStack" />
<p>
<img src="https://img.shields.io/github/languages/top/gabriel-secchi/TesteBrowserStack" />
<img src="https://img.shields.io/github/repo-size/gabriel-secchi/TesteBrowserStack" />
</p>

## Descrição
Este é uma projeto de exemplo que visa fazer a integração de upload do app e execução de testes automáticos no [BrowserStack](https://www.browserstack.com/) que é uma plataforma de emulação de devices

**Atenção: Essa integração se refere a upload de apps e execução de testes através da plataforma BrowserStack utilizando o Gradle como forma automatizada do processo.**

## Começando
Após fazer login no [BrowserStack](https://www.browserstack.com/), acesse a documentação de integração com o Gradle para obter as chaves de acesso. [Documentação](https://www.browserstack.com/docs/app-live/integrations/gradle)
Neste link só iremos precisar mesmo das chaves de acesso que estão localizadas na categoria “**Setting up**“

Tendo acesso à essas informações, seguimos para documentação real de integração que pode ser acessada pelo GitHub neste projeto [browserstack/browserstack-gradle-plugin.](https://github.com/browserstack/browserstack-gradle-plugin)<br>
Esta documentação descreve bem o processo de configuração, então é só seguir os passos, porém estarei colocando aqui também para ficar registrado.<br><br>

### Configurando o project build.gradle
- Abra seu projeto e acesse o build.gradle do projeto.<br>
- Dentro dele temos que implementar os seguintes códigos.<br>
O “**maven**”, e a dependência do BrowserStack.<br>
Este exemplo abaixo é somente a parte para a integração com o BrowserStack, você provavelmente irá ter mais código dentro do seu arquivo, então é necessário que você analise e mescle essas informações.<br>
```
buildscript {
    repositories {
        maven {
            url "https://plugins.gradle.org/m2/"
        }
    }
    dependencies {
        classpath "gradle.plugin.com.browserstack.gradle:browserstack-gradle-plugin:3.0.3"
    }
}
```

<br><br><br>
### Criando arquivo de configuração
Neste momento iremos criar um arquivo de configuração, em caso de execução de testes ele será responsável pela criação do emulador que irá rodar os testes do seu app.<br>
- Entre no modo de exibição de arquivos do projeto.<br>
- Na raiz do seu projeto crie um arquivo “**json**“ para configuração, pode ser qualquer nome, mas estarei usando como referência o seguinte nome “**browserstack.config.json**“<br>
Esse arquivo deve conter os dados abaixo, porém você pode ajusta-lo conforme sua necessidade.<br>
Caso necessário, ao lado segue a lista de devices aceitos. [Lista de devices](https://www.browserstack.com/list-of-browsers-and-platforms/app_live)
```
{
  "devices": [
    "Google Pixel 2"
  ],
  "deviceLogs": true,
  "networkLogs": true,
  "project": "Teste BrowserStack",
  "shards": {
    "numberOfShards": 3
  }
}
```

<br><br><br>
### Configurando o app build.gradle
- Abra o build.gradle do app.<br>
Neste arquivo temos que adicionar a referência para o plugin e implementar a configuração para a integração.<br>
- No início do arquivo adicione a referência do plugin, que pode ter várias formas dependendo da versão da sua aplicação, as mais comuns são:
```
plugins {
    id 'com.browserstack.gradle'
}
```
Ou
```
apply plugin: "com.browserstack.gradle"
```
Então verifique como está no seu arquivo e adicione.<br>

- Em seguida, podemos adicionar em qualquer local do arquivo, desde que esteja fora de qualquer objeto do gradle. Adicione o seguinte objeto de configuração.
```
browserStackConfig {
    username = "<seu_user_name>"
    accessKey = "<sua_access_key>"
    configFilePath = '<local_arquivo_json_de_configuracao>'
    //Exemplo: Se você colocou o arquivo na raiz do projeto como comentado anteriormente,
    //e utilizou o mesmo nome de arquivo, a propriedade "configFilePath" pode ser dessa forma.
    //configFilePath = 'browserstack.config.json'
}
```

<br><br><br>
### Fazendo upload do app
- Primeiramente, selecione a “**build variant**” que você quer utilizar.<br>
- Na sequência expanda o menu do gradle e clique em executar tarefa do gradle.<br>
- Na janela que abriu iremos rodar o seguinte comando, onde trocaremos o “**${BuildVariantName}**“ pelo nome de sua build variant.
```
gradle clean upload${BuildVariantName}ToBrowserstackAppLive
```
Para o caso de exemplo estou substituindo pela variante “**debug**“, então meu comando ficaria dessa forma.
```
gradle clean uploaddebugToBrowserstackAppLive
```
- Feito isso é só dar um “**Enter**“ e o projeto irá executar a build e em seguida o upload automático para o BrowserStack - App Live.<br>
Após a primeira execução, essa configuração irá aparecer na caixa de seleção de execução de configuração do grade.<br>

Lembre-se que se for executar o app localmente, essa configuração deve ser trocada para “**App**“<br>

Após o processo de build e upload, o app pode ser encontrado dentro da aba “**App Live**“ do BrowserStack, dentro do menu “**Uploaded Apps**“

<br><br><br>
### Rodando teste via BrowserStack
- Segue o mesmo formato do upload do app, porém ao invés do comando de “**upload**”, vamos utilizar o comando de “**execute**”<br>
```
gradle clean execute${BuildVariantName}TestsOnBrowserstack
```
Novamente temos que substituir o “**${BuildVariantName}**“ pelo nome da nossa variante de build.<br>
Dessa forma o BrowserStack irá rodar os testes criados no seu app e irá colocar o resultado de seus testes dentro da aba “**App Automate**“ lá no BrowserStack

