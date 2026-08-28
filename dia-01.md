\## Dia 1 — Sexta, 28/08



\*\*Estudei:\*\* diferença entre HTTP GET e POST. GET lê, não deveria mudar

nada no servidor. POST envia dado e muda estado.



\*\*Não entendi de primeira:\*\* por que um GET que executa uma ação (tipo

excluir conta) é perigoso — achei que o risco era "vazar dado", mas o

risco real é que qualquer coisa (crawler, imagem escondida) pode

disparar esse GET sem o usuário nem saber.



\*\*Pergunta em aberto:\*\* em quais outras situações do dia a dia eu já

vi um GET fazendo algo que deveria ser POST?



\---



\## Bandit — níveis 0 a 6



\- `ls -a` mostra arquivos ocultos (começam com `.`)

\- `cat ./-arquivo` quando o nome começa com hífen (senão o shell lê

&#x20; como opção do comando)

\- `cat "nome com espaço"` — aspas protegem nomes com espaço ou

&#x20; caractere especial

\- `file arquivo` mostra o tipo de conteúdo ANTES de dar cat (evita

&#x20; jogar binário lixo no terminal)

\- `find . -size 1033c` — busca por tamanho exato em bytes

\- `find / -user X -group Y -size Nc` — busca combinando dono, grupo

&#x20; e tamanho, a partir da raiz

