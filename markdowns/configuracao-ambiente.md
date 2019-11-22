# Configuração de ambiente

## Instalações

- [VS Code](https://code.visualstudio.com/)

## Configurando VS Code

Abrir o setting.json do VS Code e adicionar:

    "editor.formatOnSave": true

Quando salvar um arquivo, o VS Code vai formatar e identar o código para seguir um padrão definido pelo [ESLint](https://eslint.org/).

    "editor.rulers": [80,120]

Define o limite de colunas por linha.

    "editor.tabSize": 2

Define a quantidade de espaçamento da tecla TAB

    "emmet.syntaxProfiles": {
        "javascript": "jsx"
    },
    "emmet.includeLanguages": {
        "javascript": "javascriptreact"
    }

Configurar o autocomplete para JSX no formato que o React precisa.
