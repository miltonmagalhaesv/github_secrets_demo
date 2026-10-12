# Entrega — GitHub Secrets

## Problema encontrado

No workflow original (`.github/workflows/main.yml`), `MENSAGEM_SECRETA` recebia literalmente o texto `Minha mensagem secreta`. Como o workflow é versionado, esse valor ficava visível para quem pudesse ler o repositório e também podia permanecer no histórico Git. Uma senha ou token exposto poderia ser copiado e usado para acessar serviços; seria necessário revogá-lo/rotacioná-lo.

## Secret utilizado e fluxo

- **Nome do secret:** `MENSAGEM_SECRETA`.
- **Valor:** mensagem de demonstração definida nas configurações de Secrets do repositório fork; não é gravada neste documento, no código ou no workflow.
- **Fluxo:** GitHub Actions lê `secrets.MENSAGEM_SECRETA` no contexto do repositório e injeta-o apenas como variável de ambiente no passo de execução. O Node acessa o valor por `process.env.MENSAGEM_SECRETA`. O programa confirma recebimento e imprime somente o tamanho, nunca o conteúdo.
- **Configuração:** no fork, abrir **Settings → Secrets and variables → Actions → New repository secret**, usar o nome acima e definir uma mensagem própria. Não compartilhar o valor no Git ou nos logs.

## Respostas

1. Um valor literal no `pipeline.yml`/workflow é conteúdo versionado e qualquer pessoa com acesso ao arquivo pode lê-lo. Um GitHub Secret é armazenado nas configurações do repositório e fornecido à workflow no momento da execução, sem incluí-lo no código versionado.
2. Em **Settings → Secrets and variables → Actions**, como repository secret do fork.
3. Não. O código só lê o valor do ambiente, sem conter o segredo.
4. `process.env.MENSAGEM_SECRETA` consulta a variável de ambiente chamada `MENSAGEM_SECRETA` no processo Node. Se não estiver configurada, a aplicação termina com uma mensagem de erro que não inclui o valor.
5. Secrets permitem fornecer credenciais à CI/CD sem colocá-las no repositório; isso reduz vazamentos e facilita controle/rotação de credenciais. Acesso deve ser limitado e logs não devem imprimir o valor.
6. Uma senha real no YAML ficaria acessível a quem lê o repositório e no histórico de commits, e poderia ser copiada para acesso indevido. Credenciais expostas devem ser revogadas e substituídas.

## Evidência da pipeline

O workflow roda em push para `main` e também aceita execução manual (`workflow_dispatch`). Depois do cadastro do repository secret `MENSAGEM_SECRETA`, a execução manual foi concluída com sucesso: [Pipeline — execução 38187873077](https://github.com/miltonmagalhaesv/github_secrets_demo/actions/runs/38187873077). As etapas de checkout, configuração do Node, instalação de dependências e execução da aplicação passaram. A aplicação deliberadamente não revela o conteúdo do secret: a saída é `Secret recebido pela aplicação com sucesso.` seguida pela quantidade de caracteres.
