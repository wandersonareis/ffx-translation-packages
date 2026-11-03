# ffx-translation-packages

Distribuição da tradução do FINAL FANTASY X / X-2 HD Remaster.

O conteúdo versionado aqui é **apenas este README** (e o `.gitignore`). O pacote
não é commitado: ele vive **exclusivamente como asset de release** — o instalador
lê a release mais recente, baixa o pacote e o instala no jogo.

## O que existe em cada lugar

| Caminho | Conteúdo | Versionado |
|---|---|:---:|
| `README.md`, `.gitignore` | documentação e regra de ignore | sim |
| `installer_package.exe` | empacotador | não |
| `package/` | arquivos-fonte da tradução | não |
| `dist/translation-package.bin` | pacote compilado | não |
| `dist/PACKAGE-SHA256SUMS` | checksum do pacote | não |

Empacotador e arquivos-fonte não sobem para o GitHub: este repositório publica
**só o pacote**, e só como release.

## Contrato do pacote

O instalador só instala pacote no **formato v2**, com magic **`FFXX2HDR`**
(exatamente 8 caracteres ASCII). Qualquer outro magic é recusado na leitura.

## Gerar o pacote

1. Coloque os arquivos em `package/`.
2. Empacote:

```powershell
.\installer_package.exe pack -i package -o dist\translation-package.bin -v 2 --magic FFXX2HDR
```

3. Gere `dist\PACKAGE-SHA256SUMS` — **uma linha só, sem quebra de linha final**,
   no formato `<sha256>  translation-package.bin`.

## Publicar

```powershell
gh release create translation-2026.11.03 --title "Traducao 2026.11.03" --notes "..." dist\translation-package.bin dist\PACKAGE-SHA256SUMS
```

Regras:

- a tag começa com `translation-`; uso a data porque o instalador exibe a versão
  da tradução como data;
- o nome do asset **precisa** ser `translation-package.bin` — é o que o instalador
  procura (não renomeie no upload);
- o `PACKAGE-SHA256SUMS` vai **na mesma release** que o `.bin`;
- atualizar a tradução = criar uma release nova. Nenhuma versão do instalador muda.

## Como o instalador consome

1. lê a release mais recente deste repositório;
2. baixa o `PACKAGE-SHA256SUMS` e compara o SHA-256 com o do pacote instalado;
3. se for diferente, baixa o `translation-package.bin`, confere o hash e instala
   por cima da instalação anterior — sem trocar o executável do instalador.

Quando a API do GitHub estiver indisponível (ex.: HTTP 429), o instalador pode
usar uma fonte alternativa definida no `config.json` dele.
