# Minha pequena internet

Laboratório do trabalho semestral de **Redes e Sistemas Distribuídos** —
Prof. Me. Luiz Ricardo Mantovani da Silva.

Uma topologia só, que cresce ao longo do semestre: começa com duas redes que
não se falam e termina com um serviço replicado, roteamento que se refaz
sozinho e a demonstração de por que uma senha não deve viajar sem proteção.

Tudo roda no **Google Cloud Shell**, que é gratuito, não pede cartão de crédito
e dá a cada aluno uma máquina Linux com root só dela.

---

## Começando

Construa a imagem do laboratório — uma vez por sessão do Cloud Shell:

```bash
make base
```

Suba a topologia da primeira entrega:

```bash
make up E=1
```

Rode as provas:

```bash
make verificar E=1
```

`make verificar` **começa vermelho de propósito**. O laboratório vem
incompleto: o roteiro de verificação é o enunciado de verdade, e o trabalho é
levá-lo ao verde.

Enunciado de cada entrega em [`entregas/`](entregas/).

---

## E1 — Plano de endereçamento

A primeira entrega utiliza dois segmentos IPv4 independentes, cada um com uma rede `/24`.

| Segmento | Rede | Capacidade | Máquinas |
|---|---|---:|---|
| A | `10.0.10.0/24` | 254 hosts | `host-a1`, `host-a2`, `srv-a` |
| B | `10.0.20.0/24` | 254 hosts | `host-b1`, `host-b2` |

### Endereços atribuídos

| Máquina | Segmento | Endereço IPv4 |
|---|---|---|
| `e1-host-a1` | A | `10.0.10.10/24` |
| `e1-host-a2` | A | `10.0.10.11/24` |
| `e1-srv-a` | A | `10.0.10.20/24` |
| `e1-host-b1` | B | `10.0.20.10/24` |
| `e1-host-b2` | B | `10.0.20.11/24` |

Em uma rede `/24`, existem 256 endereços no total. O primeiro endereço, como `10.0.10.0`, é o endereço da rede e não pode ser atribuído a um host. O último, `10.0.10.255`, é o endereço de broadcast e também não pode ser atribuído a um host. Portanto, ficam 254 endereços utilizáveis para máquinas, de `10.0.10.1` a `10.0.10.254`. O mesmo raciocínio vale para o segmento B.

Os segmentos A e B são redes distintas: `10.0.10.0/24` e `10.0.20.0/24`. Nesta etapa não existe roteador entre elas e os hosts estão sem rota padrão. Por isso, um host do segmento A não possui uma rota de camada 3 para a rede B, resultando em `Network is unreachable`. Da mesma forma, o segmento B não consegue alcançar o servidor HTTP localizado no segmento A.

A comunicação dentro de cada segmento funciona normalmente, enquanto a comunicação entre os segmentos permanece isolada, conforme exigido pela E1.

---

## As cinco entregas

## Regras do jogo

**A entrega é o repositório, nunca o ambiente.** A correção é feita clonando o
repositório do grupo numa Cloud Shell limpa e rodando `make up` e
`make verificar`. Se não subir lá, não entregou.

**Evidência é obrigatória.** `make evidencias E=<n>` grava a saída da
verificação; os `.pcap` das entregas 2 e 5 vão junto. Isso é versionado — nunca
apaguem a pasta `evidencias/`.

**Imagem pequena.** A máquina do Cloud Shell é efêmera e recicla o cache de
imagens: só a pasta pessoal (5 GB) sobrevive. Por isso a base é Alpine.

**Nada de varredura de rede.** Os termos de uso do Cloud Shell proíbem
explicitamente varredura, e a conta que descumprir é desligada. Todo o trabalho
aqui é captura passiva dentro da rede virtual do próprio grupo — o que é outra
coisa, e é permitido.

---

## Para o professor

Antes de liberar para a turma, uma vez, numa Cloud Shell limpa:

```bash
make autoteste
```

Confere as quatro coisas de que o laboratório depende e que documentação
nenhuma garante: criação de bridge com sub-rede própria, `tcpdump` com
`NET_RAW` efetivo, `net.ipv4.ip_forward` por contêiner, e o pacote `bird` no
Alpine (só a E4 depende dele).

### Botão "Abrir no Cloud Shell"

Para cada entrega, na página do trabalho:

```
https://shell.cloud.google.com/cloudshell/editor
  ?cloudshell_git_repo=https://github.com/LuizRMSilva1973/redes-lab
  &cloudshell_tutorial=entregas/E1.md
  &show=ide%2Cterminal
```

O aluno cai no terminal com o repositório clonado e o enunciado aberto como
roteiro guiado ao lado. Trocar `E1.md` pela entrega da vez.

O repositório precisa estar em **GitHub ou Bitbucket**, público.

### Correção

```bash
git clone <repo-do-grupo> && cd <repo> && make up E=2 && make verificar E=2
```

Os roteiros imprimem o valor observado em cada ponto, não só passou/falhou —
dá para corrigir lendo a saída.
