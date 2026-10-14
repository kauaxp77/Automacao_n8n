# Apresentação para o cliente

`como-funciona.pdf`: o fluxo em 3 páginas, sem termos técnicos, com a situação de cada parte (pronto, em montagem, depende do cliente).

Para mudar: edite `como-funciona.html` e gere o PDF de novo com o Chromium:

```bash
chromium --headless=new --no-pdf-header-footer --print-to-pdf=como-funciona.pdf como-funciona.html
```

A tabela "O que está incluído" (página 3) lista o que vem pronto, o que depende do cliente e as próximas etapas: ajuste ao que for vendido.

## Guia de uso do responsável

`guia-do-cliente.pdf`: 2 páginas para o responsável usar o robô no dia a dia. Explica como mandar pedidos, as três respostas, as cores na agenda, os avisos, os comandos, o que fazer quando algo dá errado e o formulário de reserva.

**Antes de entregar,** troque em `guia-do-cliente.html` os campos em amarelo: o nome da empresa, o número do robô, o endereço do formulário e o contato de suporte. Depois gere o PDF de novo, com o mesmo comando acima (trocando os nomes dos arquivos). O usuário e a senha do formulário **não** entram no guia: vão para o cliente à parte.
