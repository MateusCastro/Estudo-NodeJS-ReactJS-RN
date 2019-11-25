# Configuração de ambiente

## Instalações

- [VS Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/)
- [Yarn](https://yarnpkg.com/)

## Configurando VS Code

### Abrir o setting.json do VS Code e adicionar:

```
"editor.formatOnSave": true
```

Quando salvar um arquivo, o VSCode vai formatar e identar o código para seguir um padrão definido pelo [ESLint](https://eslint.org/).

---

```
"editor.rulers": [80,120]
```

Define o limite de colunas por linha.

---

```
"editor.tabSize": 2
```

Define a quantidade de espaçamento da tecla TAB

---

```
"emmet.syntaxProfiles": {
    "javascript": "jsx"
},
"emmet.includeLanguages": {
    "javascript": "javascriptreact"
}
```

Configura o autocomplete para JSX no formato que o React precisa.

---

setting.json do VSCode completo:

```
{
    "window.zoomLevel": 0,
    "editor.formatOnSave": true,
    "editor.rulers": [
        80,
        120
    ],
    "editor.tabSize": 2,
    "emmet.syntaxProfiles": {
        "javascript": "jsx"
    },
    "emmet.includeLanguages": {
        "javascript": "javascriptreact"
    },
    "editor.renderLineHighlight": "gutter"
}
```

---

## Plugins VSCode

- [Color Highligth](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight)
- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
- [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
- [Rocketseat ReactJS](https://marketplace.visualstudio.com/items?itemName=rocketseat.RocketseatReactJS)
- [RocketSeat React Native](https://marketplace.visualstudio.com/items?itemName=rocketseat.RocketseatReactNative)
