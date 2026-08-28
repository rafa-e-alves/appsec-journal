# Dia 1 — Sexta, 28/08

## HTTP

- Protocolo que permite a obtenção de recurso, como documentos HTML.
- Protocolo cliente-servidor.

```
Web document
 ├── GET index.html ──┐
 ├── GET style.css  ──┼──► Web server
 ├── GET header.png ──┘
 ├── GET video.mp4  ──────► Video server
 └── GET advert.jpg ──────► Ad server
```

- Mensagens do cliente = solicitações (requests)
- Mensagens do servidor = respostas (responses)

**Camadas da Web:**

```
Client             → HTML | CSS | Web APIs / JavaScript
                           ↑ HTTP ↑
Application/Transp → DNS | UDP | TCP | TLS
IP layer           → IP
```

- O HTTP atua na camada de aplicação e é enviado sobre o protocolo TCP, ou em uma conexão TCP criptografada com TLS.

### Componentes do sistema baseados em HTTP

- O HTTP é um protocolo cliente-servidor: as requisições são enviadas por uma entidade, o agente-usuário (ou um proxy em nome dele).
- Cada requisição individual é enviada para um servidor que fornecerá um resultado, chamado resposta.
- Entre a solicitação e a resposta existem várias entidades, designadas coletivamente como **proxies**, que executam operações diferentes e atuam como gateways (intermediários) ou caches, por exemplo.
- Existem muitos outros computadores entre o navegador e o servidor que está tratando a requisição: existem roteadores, modems e muito mais.
- Graças ao modelo de camadas da Web, essas funcionalidades estão escondidas nas camadas de rede e transporte, respectivamente. O HTTP está no topo da camada de aplicação.

```
Client → Proxy → Proxy → Server
Client ← Proxy ← Proxy ← Server
```

---

## Mensagens HTTP

### Requisições

```
GET  /  HTTP/1.1
Host: developer.mozilla.org
Accept-Language: fr
```
(*método* — *caminho* — *versão do protocolo*, depois os *headers*)

**Elementos:**

- Um método HTTP, geralmente um verbo como GET, POST, DELETE, PUT, etc, ou um substantivo como OPTIONS ou HEAD, que define qual operação o cliente quer fazer.
Tipicamente, um cliente quer pegar um recurso (usando GET) ou publicar dados de um formulário HTML (usando POST).
- O caminho do recurso a ser buscado; a URL do recurso sem os elementos que são de contexto, por exemplo sem o protocolo (`http://`), o domínio (aqui `developer.mozilla.org`), ou a porta TCP (aqui indicada pelo `80`, que é oculto por ser o número da porta padrão).
- A versão do protocolo HTTP.
- Cabeçalhos opcionais.
- Ou um corpo de dados.

### Respostas

```
HTTP/1.1  200  OK
Date: Tue, 18 Jun 2024 10:03:55 GMT
Cache-Control: public, max-age: 3600
Content-Type: text/html
```
(*versão do protocolo* — *status code* — *status message*, depois os *headers*)

**Elementos:**

- A versão do protocolo HTTP que elas seguem.
- Um código de status, indicando se a requisição foi bem sucedida, ou não, e por quê.
- Uma mensagem de status, uma pequena descrição informal sobre o código de status.
- Cabeçalhos HTTP, como aqueles de requisições.
- Opcionalmente, um corpo com dados do recurso requisitado.

---

## Métodos de Requisição HTTP

O protocolo HTTP define um conjunto de métodos de requisição responsáveis por indicar a ação a ser executada para um dado recurso. Embora esses métodos possam ser descritos como substantivos, eles também são comumente referenciados como HTTP Verbs (verbos HTTP). Cada um deles implementa uma semântica diferente, mas alguns recursos são compartilhados por um grupo deles: todo método pode ser **safe**, **idempotent** e/ou **cacheable**.

- **Safe** — não muda o estado do servidor. É só leitura.
- **Idempotent** — repetir a mesma requisição várias vezes tem o mesmo efeito que fazer uma vez só (não "acumula" ação a cada repetição).
- **Cacheable** — a resposta pode ser guardada e reaproveitada numa próxima requisição igual, sem precisar ir de novo ao servidor.

| Método | O que faz | Exemplo | Safe | Idempotent | Cacheable |
|---|---|---|---|---|---|
| **GET** | Solicita a representação de um recurso específico. Deve retornar apenas dados. | `GET /users/42` — busca os dados do usuário 42 | ✅ | ✅ | ✅ |
| **HEAD** | Solicita uma resposta idêntica ao GET, porém sem o corpo da resposta. | `HEAD /users/42` — só confirma que existe, sem trazer os dados | ✅ | ✅ | ✅ |
| **POST** | Submete uma entidade a um recurso específico, frequentemente causando mudança de estado ou efeitos colaterais. | `POST /users` — cria um novo usuário | ❌ | ❌ | ❌ (normalmente) |
| **PUT** | Substitui todas as atuais representações do recurso de destino pela carga de dados de requisição. | `PUT /users/42` — substitui o usuário 42 inteiro pelos dados enviados | ❌ | ✅ | ❌ |
| **DELETE** | Remove um recurso específico. | `DELETE /users/42` — apaga o usuário 42 | ❌ | ✅ | ❌ |
| **CONNECT** | Estabelece um túnel para o servidor identificado pelo recurso de destino. | usado por proxies pra abrir túnel HTTPS | ❌ | ❌ | ❌ |
| **TRACE** | Executa um teste de chamada loop-back junto com o caminho para o recurso de destino. | usado pra depurar o caminho que a requisição percorre | ✅ | ✅ | ❌ |
| **PATCH** | Aplica modificações parciais em um recurso. | `PATCH /users/42` — atualiza só o campo e-mail do usuário 42 | ❌ | ❌ (depende) | ❌ |

> **Por que isso importa pra segurança:** GET ser *safe* é uma promessa de design, não uma garantia técnica. Se uma aplicação implementa uma ação destrutiva (tipo excluir conta) atrás de um GET, ela quebra essa promessa — e ferramentas que assumem "GET é sempre seguro" (crawlers, proxies, o próprio navegador pré-carregando links) podem disparar a ação sem o usuário nem clicar em nada. É a raiz de ataques CSRF (semana 4).

---

## Glossário

| Sigla | Significado |
|---|---|
| HTTP | Hypertext Transfer Protocol |
| HTML | HyperText Markup Language |
| CSS | Cascading Style Sheets |
| API | Application Programming Interface |
| DNS | Domain Name System |
| UDP | User Datagram Protocol |
| TCP | Transmission Control Protocol |
| TLS | Transport Layer Security |
| IP | Internet Protocol |
| PROXY | Servidor intermediário entre o dispositivo e a internet |

---

## Não entendi de primeira

**Pergunta:** Por que um GET que executa uma ação (tipo excluir conta) é perigoso? Achei que o risco era "vazar dado".

**Resposta:** O risco real não é vazamento, é execução não intencional. GET é tratado como seguro por padrão em toda a infraestrutura da web (crawlers indexam, navegadores pré-carregam, proxies fazem cache). Se um GET na verdade executa uma ação destrutiva, qualquer coisa que "só olhe" o link — um bot do Google, uma tag `<img src="...">` escondida numa página maliciosa — dispara a ação de verdade, sem o usuário clicar em nada. A regra de ouro: GET nunca deveria ter efeito colateral.

## Pergunta em aberto

Em quais outras situações do dia a dia eu já vi um GET fazendo algo que deveria ser POST?

---

## Bandit — níveis 0 a 7

- `ls -a` mostra arquivos ocultos (começam com `.`)
- `cat ./-arquivo` quando o nome começa com hífen (senão o shell lê como opção do comando)
- `cat "nome com espaço"` — aspas protegem nomes com espaço ou caractere especial
- `file arquivo` mostra o tipo de conteúdo ANTES de dar cat (evita jogar binário lixo no terminal)
- <code>find . -size <strong>tamanho</strong>c</code> — busca por tamanho exato em bytes
- <code>find / -user <strong>dono</strong> -group <strong>grupo</strong> -size <strong>tamanho</strong>c</code> — busca por dono, grupo e tamanho, a partir da raiz
- <code>grep <strong>termo</strong> <strong>arquivo</strong></code> — busca um termo dentro de um arquivo
