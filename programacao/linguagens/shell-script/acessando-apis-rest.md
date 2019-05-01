---
description: Interaja com serviços REST!
---

# Acessando APIs Rest

## Introdução

É possível automatizar tarefas que dependam de serviços REST utilizando apenas Shell Script, com duas aplicações: [jq](https://stedolan.github.io/jq/) e [curl](https://curl.haxx.se/). A facilidade com que estes recursos podem ser utilizados me faz considerar o conhecimento destas ferramentas indispensável para qualquer desenvolvedor.

## Exemplo: Github Release

No repositório [Trello Toolkit](https://github.com/Miguel-Fontes/trello-toolkit/blob/master/util/release.sh#L117), há um Script que faz a geração de um novo release da extensão do Chrome automaticamente, utilizando-se das ferramentas curl e jq.

```bash
releaseToGithub() {
    GITHUB_RELEASE=$(curl -X POST \
      "https://api.github.com/repos/Miguel-Fontes/trello-toolkit/releases?access_token=$TOKEN" \
      -H 'cache-control: no-cache' \
      -H 'content-type: application/json' \
      -d "{
      \"tag_name\": \"$NEXT_VERSION\",
      \"target_commitish\": \"master\",
      \"name\": \"Version $NEXT_VERSION\"
    }")

    GITHUB_RELEASE_ID=$(echo $GITHUB_RELEASE | jq .id)
    GITHUB_RELEASE_URL=$(echo $GITHUB_RELEASE | jq .html_url)

    ARTIFACT_URL=$(curl -s --data-binary @"dist/trello-toolkit.tar.gz" \
      -H 'cache-control: no-cache' \
      -H 'content-type: application/octet-stream' \
      "https://uploads.github.com/repos/Miguel-Fontes/trello-toolkit/releases/$GITHUB_RELEASE_ID/assets?access_token=$TOKEN&name=trello-toolkit.tar.gz" | jq .url)

    echo "Artifact successfully uploaded to GitHub! Check the release at $GITHUB_RELEASE_URL, or download directly from $ARTIFACT_URL"
}
```

{% hint style="info" %}
O [Trello Toolkit](https://github.com/Miguel-Fontes/trello-toolkit) é um projeto pessoal, uma extensão do chrome para adicionar alguns recursos que facilitem a vida utilizando o Trello. É também um [breakable toy](https://www.oreilly.com/library/view/apprenticeship-patterns/9780596806842/ch05s03.html) para estudar Javascript e entender como extensoes do Chrome funcionam..
{% endhint %}

Executando um request para a API de Github Releases utilizando-se do curl, salvamos o JSON de resposta na variável `GITHUB_RELEASE`. Nas duas linhas seguintes, obtemos desta variável os campos `id` e `html_url`  utilizando do jq.

O jq busca os valores dentro de um JSON utilizando filtros: expressões que definem a forma de tratamento do objeto JSON, e produzindo um resultado final. Existem diversas possibilidades listadas no [manual do jq](https://stedolan.github.io/jq/manual/), e a mais simples é a utilizada aqui: encontrar um atributo específico. Para isto, definimos o caminho para o atributo com um "dot notation", `.id` e `.html_url`. 

{% hint style="info" %}
Se o atributo estivesse em outro nível, poderíamos também "caminhar" pela estrutura indicando os nívels  `jq. atrributo.atributo.valor`.
{% endhint %}

Por último, enviamos o artefato à ser anexado ao Github release e pronto! O release está no GitHub. Neste último curl, aproveito para buscar a URL do novo artefato do JSON retornado, fazendo um pipe \(`|`\) para o jq e obtendo o campo `url`.

{% hint style="info" %}
A chave `-s` do comando `curl` é a opção _silent_ fazendo com que ele não exiba mensagens com status da transferência de dados. Quando a chave `-s` -e usada, mensagens de erro também não serão exibidas. Para evitar este comportamento, utilize a opção `-S`.  Esta é uma combinação muito comum e geralmente escrita  como `curl -sS`!
{% endhint %}

## Referências e links

* [jq](https://stedolan.github.io/jq/), um processador de JSON leve e flexível na linha de comando. É a principal ferramenta utlizada para trabalhar com JSONs em um script SH.
* [curl](https://curl.haxx.se/): uma library e ferramenta de linha de comando para transferência de dados utilizando urls.

