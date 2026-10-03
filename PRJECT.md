# TUTORIAL ELD310 — CLIQUE A CLIQUE
## Cisco Packet Tracer 9.0.1 — Projeto de Interconexão de Redes e Serviços

> **LEIA ISTO PRIMEIRO:** este tutorial assume que você **nunca** usou o Packet Tracer.
> Cada passo diz exatamente onde clicar, o que arrastar e o que digitar.
> Faça **na ordem**, de cima para baixo, sem pular nada.

---

# PARTE 0 — Conhecendo a tela do Packet Tracer

Quando o programa abre, a tela tem estas áreas. Decore os nomes porque vou usá-los o tempo todo:

```
┌──────────────────────────────────────────────────────────────┐
│  File  Edit  Options  View  Tools  Extensions  Help          │ ← MENU SUPERIOR
├──────────────────────────────────────────────────────────────┤
│  [ícones: Novo, Abrir, Salvar, Imprimir...]                  │ ← BARRA DE FERRAMENTAS
├───┬──────────────────────────────────────────────────────┬───┤
│   │                                                      │ ▲ │
│   │                                                      │ 🖐 │ ← BARRA DIREITA
│   │              ÁREA DE TRABALHO                        │ 📝 │   (setinha, mãozinha,
│   │        (aqui você monta a rede)                      │ ❌ │    nota, deletar,
│   │                                                      │ 🔍 │    inspecionar, envelope)
│   │                                                      │ ✉ │
├───┴──────────────────────────────────────────────────────┴───┤
│ [Network Devices] [End Devices] [Components] [Connections]   │ ← CATEGORIA PRINCIPAL
│ [Routers][Switches][Hubs][Wireless][Security][WAN Emulation] │ ← SUBCATEGORIA
│  🖥️ 🖥️ 🖥️  (os equipamentos aparecem aqui pra você arrastar)  │ ← CAIXA DE EQUIPAMENTOS
└──────────────────────────────────────────────────────────────┘
```

**Como colocar QUALQUER equipamento na tela (memorize este movimento):**
1. Clique numa **categoria principal** (canto inferior esquerdo)
2. Clique numa **subcategoria** (a linha logo abaixo)
3. Na **caixa de equipamentos** à direita, **clique** no equipamento desejado
4. Depois **clique** num ponto vazio da área de trabalho → o equipamento aparece ali

> Se passar o mouse em cima de um equipamento na caixa, aparece o **nome dele** numa
> etiqueta amarela. Use isso pra achar o modelo certo.

**Como renomear um equipamento:**
- Clique **uma vez** no texto do nome que fica **embaixo** do ícone (ex.: `Router0`)
- O texto vira uma caixinha editável → apague e digite o novo nome → clique fora

---

# PARTE 1 — Os cálculos das sub-redes (para o PDF de entrega)

## 1.1 LAN1 (amarela) — `120.20.30.0/24` em 2 sub-redes

| | Sub-rede 01 | Sub-rede 02 (server farm) |
|---|---|---|
| Capacidade pedida | 110 máquinas | 31 máquinas |
| Bits de host necessários | 7 ($2^7-2=126 \ge 110$) | 6 ($2^6-2=62 \ge 31$) |
| **Máscara** | **/25** = `255.255.255.128` | **/26** = `255.255.255.192` |
| **Endereço da rede (prefixo)** | `120.20.30.0` | `120.20.30.128` |
| **Primeiro IP usável** | `120.20.30.1` | `120.20.30.129` |
| **Último IP usável** | `120.20.30.126` | `120.20.30.190` |
| **Broadcast** | `120.20.30.127` | `120.20.30.191` |
| Hosts disponíveis | 126 | 62 |

*(sobra `120.20.30.192/26` sem uso)*

## 1.2 LAN2 (azul) — `192.168.0.0/16` em 4 sub-redes iguais

4 sub-redes = 2 bits emprestados → `/16 + 2` = **`/18`** = `255.255.192.0`

| Sub-rede | Prefixo | 1º IP usável | Último IP usável | Broadcast | Usar? |
|---|---|---|---|---|---|
| **A** | `192.168.0.0` | `192.168.0.1` | `192.168.63.254` | `192.168.63.255` | **SIM** |
| **B** | `192.168.64.0` | `192.168.64.1` | `192.168.127.254` | `192.168.127.255` | **SIM** |
| C | `192.168.128.0` | `192.168.128.1` | `192.168.191.254` | `192.168.191.255` | não |
| D | `192.168.192.0` | `192.168.192.1` | `192.168.255.254` | `192.168.255.255` | não |

## 1.3 LAN3 (verde) — `10.0.0.0/18` (não dividida)

| | Valor |
|---|---|
| Máscara | `255.255.192.0` (/18) |
| Prefixo | `10.0.0.0` |
| 1º IP usável | `10.0.0.1` |
| Último IP usável | `10.0.63.254` |
| Broadcast | `10.0.63.255` |

## 1.4 Links WAN entre roteadores (precisam existir pro RIP funcionar)

| Link | Rede | Máscara | IPs |
|---|---|---|---|
| R1 ↔ R2 | `172.16.1.0/30` | `255.255.255.252` | R1=`172.16.1.1` · R2=`172.16.1.2` |
| R2 ↔ R3 | `172.16.2.0/30` | `255.255.255.252` | R2=`172.16.2.1` · R3=`172.16.2.2` |

## 1.5 Tabela mestre de IPs

| Equipamento | IP | Máscara | Gateway | DNS |
|---|---|---|---|---|
| R1 G0/0 | `120.20.30.1` | `255.255.255.128` | — | — |
| R1 G0/1 | `120.20.30.129` | `255.255.255.192` | — | — |
| R1 Se0/3/0 | `172.16.1.1` | `255.255.255.252` | — | — |
| R2 G0/0 | `192.168.0.1` | `255.255.192.0` | — | — |
| R2 G0/1 | `192.168.64.1` | `255.255.192.0` | — | — |
| R2 Se0/3/0 | `172.16.1.2` | `255.255.255.252` | — | — |
| R2 Se0/3/1 | `172.16.2.1` | `255.255.255.252` | — | — |
| R3 G0/0 | `10.0.0.1` | `255.255.192.0` | — | — |
| R3 Se0/3/0 | `172.16.2.2` | `255.255.255.252` | — | — |
| PC1 | `120.20.30.10` | `255.255.255.128` | `120.20.30.1` | `120.20.30.130` |
| PC2 | `120.20.30.11` | `255.255.255.128` | `120.20.30.1` | `120.20.30.130` |
| PC3 | `120.20.30.12` | `255.255.255.128` | `120.20.30.1` | `120.20.30.130` |
| PC4 | `120.20.30.13` | `255.255.255.128` | `120.20.30.1` | `120.20.30.130` |
| SRV-DNS | `120.20.30.130` | `255.255.255.192` | `120.20.30.129` | `120.20.30.130` |
| SRV-HTTP | `120.20.30.131` | `255.255.255.192` | `120.20.30.129` | `120.20.30.130` |
| PC5 | `192.168.0.10` | `255.255.192.0` | `192.168.0.1` | `120.20.30.130` |
| PC6 | `192.168.0.11` | `255.255.192.0` | `192.168.0.1` | `120.20.30.130` |
| PC7 | `192.168.64.10` | `255.255.192.0` | `192.168.64.1` | `120.20.30.130` |
| PC8 | `192.168.64.11` | `255.255.192.0` | `192.168.64.1` | `120.20.30.130` |
| SRV-DHCP | `10.0.0.2` | `255.255.192.0` | `10.0.0.1` | `120.20.30.130` |
| PC9 a PC12 | automático (DHCP) | — | — | — |
| Laptop1 a 3 | automático (DHCP) | — | — | — |

---

# PARTE 2 — Colocando os 3 ROTEADORES na tela

## 2.1 Colocar o roteador R1

1. No canto **inferior esquerdo**, clique em **`Network Devices`** (ícone de roteador/switch)
2. Na linha de baixo que apareceu, clique em **`Routers`**
3. Na caixa da direita, passe o mouse nos ícones até achar o que a etiqueta diz **`2911`**
4. **Clique** no ícone `2911`
5. Agora **clique** num ponto vazio da área de trabalho, no **canto superior esquerdo**
6. Um roteador chamado `Router0` apareceu ali

**Renomear:**
7. Clique **uma vez** no texto `Router0` que está embaixo do ícone
8. Apague tudo e digite **`R1`**
9. Clique num espaço vazio da tela para confirmar

## 2.2 Colocar R2 e R3

10. Repita os passos 4 e 5 → coloque outro 2911 no **meio da tela** → renomeie para **`R2`**
11. Repita de novo → coloque outro 2911 na **direita da tela** → renomeie para **`R3`**

Você deve ter agora: `R1` (esquerda), `R2` (meio), `R3` (direita).

## 2.3 Instalar a placa serial (HWIC-2T) — FAÇA NOS 3 ROTEADORES

> Sem essa placa você **não consegue** ligar roteador em roteador.

### Para o R1:

1. **Clique duas vezes** no ícone do `R1` → abre uma janela
2. No topo dessa janela, clique na aba **`Physical`**
3. Você verá:
   - **Esquerda:** lista `MODULES` com nomes tipo `HWIC-1GE-SFP`, `HWIC-2T`, `HWIC-4ESW`...
   - **Direita:** a foto do roteador (o chassi)
4. Na foto do roteador, ache o **botão redondo de liga/desliga** — fica no **lado direito** do
   painel frontal, é um circulozinho pequeno
5. **Clique nesse botão** → a luz do roteador apaga (ele desligou). **Isso é obrigatório**, senão
   o Packet Tracer não deixa instalar o módulo
6. Na lista `MODULES` da esquerda, clique em **`HWIC-2T`**
   - Vai aparecer uma descrição embaixo: *"The HWIC-2T is a 2-port serial..."*
7. **Arraste** o `HWIC-2T` da lista até um dos **slots retangulares vazios** na foto do roteador
   (os slots pretos vazios no lado direito da foto)
8. Solte → o módulo encaixa e aparecem 2 conectores seriais
9. **Clique de novo** no botão de liga/desliga → o roteador liga
10. **Feche** a janela (X no canto superior direito da janela)

### Para o R2 e o R3:

11. Repita **exatamente** os passos 1 a 10 no `R2`
12. Repita **exatamente** os passos 1 a 10 no `R3`

---

# PARTE 3 — Colocando os 7 SWITCHES

1. Canto inferior esquerdo → clique em **`Network Devices`**
2. Na linha de baixo → clique em **`Switches`**
3. Na caixa da direita, ache o ícone com etiqueta **`2960`** e clique nele
4. Clique na área de trabalho para posicionar

Coloque **7 switches** nestas posições aproximadas e renomeie cada um
(lembre: clique no nome embaixo do ícone para renomear):

| Nome | Onde posicionar | Vai servir para |
|---|---|---|
| `SW1` | abaixo e à esquerda do R1 | LAN1 sub-rede 01 (switch principal) |
| `SW2` | abaixo do SW1 | LAN1 sub-rede 01 (switch de acesso) |
| `SW3` | acima do R1 | LAN1 sub-rede 02 (distribuição) |
| `SW4` | acima do SW3 | LAN1 sub-rede 02 (**server farm**) |
| `SW5` | abaixo e à esquerda do R2 | LAN2 sub-rede A |
| `SW6` | abaixo e à direita do R2 | LAN2 sub-rede B |
| `SW7` | abaixo do R3 | LAN3 |

---

# PARTE 4 — Colocando PCs, LAPTOPS e SERVIDORES

## 4.1 Os 12 PCs

1. Canto inferior esquerdo → clique em **`End Devices`**
2. Na linha de baixo → clique em **`End Devices`** de novo
3. Na caixa da direita, o **primeiro ícone** é o `PC` (etiqueta: *"PC"*) → clique nele
4. Clique na área de trabalho → apareceu `PC0`

Coloque 12 PCs e renomeie:

| Nome | Onde posicionar |
|---|---|
| `PC1`, `PC2` | abaixo do SW1 |
| `PC3`, `PC4` | abaixo do SW2 |
| `PC5`, `PC6` | abaixo do SW5 |
| `PC7`, `PC8` | abaixo do SW6 |
| `PC9`, `PC10`, `PC11`, `PC12` | abaixo do SW7 |

## 4.2 Os 3 servidores

1. Ainda em **`End Devices`** → **`End Devices`**
2. Ache o ícone com etiqueta **`Server`** (parece um gabinete/rack) → clique
3. Clique na tela → renomeie

| Nome | Onde posicionar |
|---|---|
| `SRV-DNS` | acima do SW4 (topo da tela, perto do R1) |
| `SRV-HTTP` | acima do SW4, ao lado do SRV-DNS |
| `SRV-DHCP` | ao lado do SW7 (perto do R3) |

## 4.3 Os 3 laptops

1. Ainda em **`End Devices`** → **`End Devices`**
2. Ache o ícone com etiqueta **`Laptop`** → clique
3. Clique na tela → renomeie

| Nome | Onde posicionar |
|---|---|
| `Laptop1`, `Laptop2`, `Laptop3` | bem à direita, longe do SW7 (vão ser sem fio) |

## 4.4 O Access Point

1. Canto inferior esquerdo → clique em **`Network Devices`**
2. Na linha de baixo → clique em **`Wireless Devices`**
3. Ache o ícone com etiqueta **`AccessPoint-PT`** → clique
4. Clique na tela, **entre o SW7 e os laptops**
5. Renomeie para **`AP-LAN3`**

---

# PARTE 5 — CABEANDO TUDO (a parte que mais dá erro — leia devagar)

## 5.1 Como usar a ferramenta de cabo

1. Canto inferior esquerdo → clique em **`Connections`** (ícone de **raio ⚡ amarelo**)
2. A caixa da direita mostra os tipos de cabo. Passe o mouse para ver a etiqueta de cada um.
   Os que você vai usar:

| Ícone / etiqueta | Quando usar |
|---|---|
| **`Copper Straight-Through`** (linha preta **contínua**) | Roteador↔Switch, PC↔Switch, Servidor↔Switch, AP↔Switch |
| **`Copper Cross-Over`** (linha preta **tracejada**) | Switch↔Switch |
| **`Serial DCE`** (linha com **reloginho**) | Roteador↔Roteador |

3. **Clique** no tipo de cabo desejado (o cursor vira um conector)
4. **Clique no primeiro equipamento** → abre uma listinha com os nomes das portas
5. **Clique na porta** que você quer usar
6. **Clique no segundo equipamento** → abre a listinha de portas dele
7. **Clique na porta** → o cabo é criado

> **Bolinha verde** nas duas pontas = link OK.
> **Bolinha vermelha** = link com problema (cabo errado ou interface desligada).
> **Triângulo laranja** = switch calculando o Spanning-Tree (**espere ~30 s**, vira verde sozinho).

## 5.2 Cabos da LAN1

Selecione **`Copper Straight-Through`** e faça:

| Nº | Clique 1 | Porta | Clique 2 | Porta |
|---|---|---|---|---|
| 1 | `R1` | `GigabitEthernet0/0` | `SW1` | `FastEthernet0/1` |
| 2 | `PC1` | `FastEthernet0` | `SW1` | `FastEthernet0/3` |
| 3 | `PC2` | `FastEthernet0` | `SW1` | `FastEthernet0/4` |
| 4 | `PC3` | `FastEthernet0` | `SW2` | `FastEthernet0/2` |
| 5 | `PC4` | `FastEthernet0` | `SW2` | `FastEthernet0/3` |
| 6 | `R1` | `GigabitEthernet0/1` | `SW3` | `FastEthernet0/1` |
| 7 | `SRV-DNS` | `FastEthernet0` | `SW4` | `FastEthernet0/2` |
| 8 | `SRV-HTTP` | `FastEthernet0` | `SW4` | `FastEthernet0/3` |

Agora troque para **`Copper Cross-Over`** (o tracejado) e faça:

| Nº | Clique 1 | Porta | Clique 2 | Porta |
|---|---|---|---|---|
| 9 | `SW1` | `FastEthernet0/2` | `SW2` | `FastEthernet0/1` |
| 10 | `SW3` | `FastEthernet0/2` | `SW4` | `FastEthernet0/1` |

## 5.3 Cabos da LAN2

Selecione **`Copper Straight-Through`**:

| Nº | Clique 1 | Porta | Clique 2 | Porta |
|---|---|---|---|---|
| 11 | `R2` | `GigabitEthernet0/0` | `SW5` | `FastEthernet0/1` |
| 12 | `PC5` | `FastEthernet0` | `SW5` | `FastEthernet0/2` |
| 13 | `PC6` | `FastEthernet0` | `SW5` | `FastEthernet0/3` |
| 14 | `R2` | `GigabitEthernet0/1` | `SW6` | `FastEthernet0/1` |
| 15 | `PC7` | `FastEthernet0` | `SW6` | `FastEthernet0/2` |
| 16 | `PC8` | `FastEthernet0` | `SW6` | `FastEthernet0/3` |

## 5.4 Cabos da LAN3

Ainda com **`Copper Straight-Through`**:

| Nº | Clique 1 | Porta | Clique 2 | Porta |
|---|---|---|---|---|
| 17 | `R3` | `GigabitEthernet0/0` | `SW7` | `FastEthernet0/1` |
| 18 | `SRV-DHCP` | `FastEthernet0` | `SW7` | `FastEthernet0/2` |
| 19 | `PC9` | `FastEthernet0` | `SW7` | `FastEthernet0/3` |
| 20 | `PC10` | `FastEthernet0` | `SW7` | `FastEthernet0/4` |
| 21 | `PC11` | `FastEthernet0` | `SW7` | `FastEthernet0/5` |
| 22 | `PC12` | `FastEthernet0` | `SW7` | `FastEthernet0/6` |
| 23 | `AP-LAN3` | `Port 0` | `SW7` | `FastEthernet0/7` |

> Os **laptops NÃO levam cabo** — eles vão se conectar por Wi-Fi na Parte 9.

## 5.5 Cabos SERIAIS entre os roteadores

Selecione **`Serial DCE`** (o que tem o desenho de relógio).

> ⚠️ **REGRA IMPORTANTE:** o roteador em que você clica **PRIMEIRO** vira o lado **DCE**,
> e é nele que você vai digitar o comando `clock rate`. Siga a ordem exata abaixo.

| Nº | Clique 1 (vira DCE) | Porta | Clique 2 | Porta |
|---|---|---|---|---|
| 24 | **`R1`** | `Serial0/3/0` | `R2` | `Serial0/3/0` |
| 25 | **`R2`** | `Serial0/3/1` | `R3` | `Serial0/3/0` |

> Os links seriais vão ficar **vermelhos** agora. É normal — eles ficam verdes depois
> que você configurar o `clock rate` e o `no shutdown` na Parte 6.

---

# PARTE 6 — CONFIGURANDO OS ROTEADORES (linha de comando)

## 6.1 Como abrir a linha de comando de um roteador

1. **Clique duas vezes** no roteador → abre a janela
2. Clique na aba **`CLI`** (no topo da janela, ao lado de `Physical` e `Config`)
3. Espere o texto de boot terminar (uns 10 segundos)
4. Vai aparecer: `Would you like to enter the initial configuration dialog? [yes/no]:`
5. Digite **`no`** e aperte **Enter**
6. Aperte **Enter** de novo → aparece o prompt `Router>`
7. Agora é só **copiar o bloco abaixo e colar** (Ctrl+V) dentro da janela preta

> **Dica:** se o texto travar e aparecer `--More--`, aperte a **barra de espaço**.
> Se aparecer `Translating "xxx"...domain server`, aperte **Ctrl+Shift+6** para cancelar.

## 6.2 Configuração do R1 — copie e cole TUDO de uma vez

```cisco
enable
configure terminal
hostname R1
no ip domain-lookup
interface GigabitEthernet0/0
 description LAN1-Sub-rede-01
 ip address 120.20.30.1 255.255.255.128
 no shutdown
 exit
interface GigabitEthernet0/1
 description LAN1-Sub-rede-02-ServerFarm
 ip address 120.20.30.129 255.255.255.192
 no shutdown
 exit
interface Serial0/3/0
 description WAN-para-R2
 ip address 172.16.1.1 255.255.255.252
 clock rate 64000
 no shutdown
 exit
router rip
 version 2
 no auto-summary
 network 120.0.0.0
 network 172.16.0.0
 exit
end
write memory
```

Quando terminar, ele mostra `[OK]`. Feche a janela.

## 6.3 Configuração do R2 — copie e cole TUDO

```cisco
enable
configure terminal
hostname R2
no ip domain-lookup
interface GigabitEthernet0/0
 description LAN2-Sub-rede-A
 ip address 192.168.0.1 255.255.192.0
 no shutdown
 exit
interface GigabitEthernet0/1
 description LAN2-Sub-rede-B
 ip address 192.168.64.1 255.255.192.0
 no shutdown
 exit
interface Serial0/3/0
 description WAN-para-R1
 ip address 172.16.1.2 255.255.255.252
 no shutdown
 exit
interface Serial0/3/1
 description WAN-para-R3
 ip address 172.16.2.1 255.255.255.252
 clock rate 64000
 no shutdown
 exit
router rip
 version 2
 no auto-summary
 network 192.168.0.0
 network 192.168.64.0
 network 172.16.0.0
 exit
end
write memory
```

## 6.4 Configuração do R3 (com a ACL) — copie e cole TUDO

```cisco
enable
configure terminal
hostname R3
no ip domain-lookup
interface GigabitEthernet0/0
 description LAN3-rede-movel
 ip address 10.0.0.1 255.255.192.0
 no shutdown
 exit
interface Serial0/3/0
 description WAN-para-R2
 ip address 172.16.2.2 255.255.255.252
 no shutdown
 exit
router rip
 version 2
 no auto-summary
 network 10.0.0.0
 network 172.16.0.0
 exit
ip access-list extended BLOQUEIA-LAN2
 deny ip 10.0.0.0 0.0.63.255 192.168.0.0 0.0.255.255
 permit ip any any
 exit
interface GigabitEthernet0/0
 ip access-group BLOQUEIA-LAN2 in
 exit
end
write memory
```

**O que essa ACL faz:** a linha `deny` joga fora todo pacote que sai da LAN3 (`10.0.0.0/18`)
e vai para a LAN2 (`192.168.0.0/16`). A linha `permit ip any any` libera todo o resto
(inclusive LAN3 → LAN1, que é o que o professor pediz que funcione).

## 6.5 Conferir se ficou tudo certo

Depois de configurar os 3, os cabos seriais devem ficar **verdes**. Se ainda estiverem vermelhos:

1. Abra o CLI do roteador
2. Digite: `show ip interface brief` e Enter
3. Todas as interfaces usadas devem estar `up` / `up`
4. Se aparecer `administratively down`, faltou o `no shutdown`

---

# PARTE 7 — CONFIGURANDO OS PCs (IP manual)

## 7.1 Como configurar o IP de um PC

1. **Clique duas vezes** no PC → abre a janela
2. Clique na aba **`Desktop`** (no topo da janela)
3. Clique no ícone **`IP Configuration`** (primeiro da lista, parece uma engrenagem/rede)
4. Marque a bolinha **`Static`** (não deixe em `DHCP`)
5. Preencha os 4 campos:
   - `IPv4 Address`
   - `Subnet Mask`
   - `Default Gateway`
   - `DNS Server`
6. **Feche a janela** (não tem botão salvar — ele salva sozinho)

## 7.2 PCs da LAN1 — preencha assim

**PC1:**
```
IPv4 Address    : 120.20.30.10
Subnet Mask     : 255.255.255.128
Default Gateway : 120.20.30.1
DNS Server      : 120.20.30.130
```

**PC2:**
```
IPv4 Address    : 120.20.30.11
Subnet Mask     : 255.255.255.128
Default Gateway : 120.20.30.1
DNS Server      : 120.20.30.130
```

**PC3:**
```
IPv4 Address    : 120.20.30.12
Subnet Mask     : 255.255.255.128
Default Gateway : 120.20.30.1
DNS Server      : 120.20.30.130
```

**PC4:**
```
IPv4 Address    : 120.20.30.13
Subnet Mask     : 255.255.255.128
Default Gateway : 120.20.30.1
DNS Server      : 120.20.30.130
```

## 7.3 PCs da LAN2 — preencha assim

**PC5:**
```
IPv4 Address    : 192.168.0.10
Subnet Mask     : 255.255.192.0
Default Gateway : 192.168.0.1
DNS Server      : 120.20.30.130
```

**PC6:**
```
IPv4 Address    : 192.168.0.11
Subnet Mask     : 255.255.192.0
Default Gateway : 192.168.0.1
DNS Server      : 120.20.30.130
```

**PC7:**
```
IPv4 Address    : 192.168.64.10
Subnet Mask     : 255.255.192.0
Default Gateway : 192.168.64.1
DNS Server      : 120.20.30.130
```

**PC8:**
```
IPv4 Address    : 192.168.64.11
Subnet Mask     : 255.255.192.0
Default Gateway : 192.168.64.1
DNS Server      : 120.20.30.130
```

> **PC9 a PC12 ficam para depois** — eles vão pegar IP automático, mas só funciona
> depois que o servidor DHCP estiver ligado (Parte 8.1).

---

# PARTE 8 — CONFIGURANDO OS 3 SERVIDORES

## 8.1 SRV-DHCP (o da LAN3)

### Passo A — dar IP fixo ao servidor

1. **Clique duas vezes** em `SRV-DHCP`
2. Aba **`Desktop`** → ícone **`IP Configuration`**
3. Marque **`Static`** e preencha:
```
IPv4 Address    : 10.0.0.2
Subnet Mask     : 255.255.192.0
Default Gateway : 10.0.0.1
DNS Server      : 120.20.30.130
```
4. **Não feche a janela ainda**

### Passo B — ligar o serviço DHCP

5. No topo da janela, clique na aba **`Services`**
6. Na **lista da esquerda**, clique em **`DHCP`**
7. Em `Service`, marque a bolinha **`On`**
8. Na tabela de baixo, **clique na linha `serverPool`** (ela já vem criada)
   - Os campos de cima vão se preencher com os valores dela
9. Agora **altere** os campos assim:
```
Pool Name              : serverPool
Default Gateway        : 10.0.0.1
DNS Server             : 120.20.30.130
Start IP Address       : 10 . 0 . 0 . 10
Subnet Mask            : 255 . 255 . 192 . 0
Maximum Number of Users: 100
TFTP Server            : 0.0.0.0
WLC Address            : 0.0.0.0
```
10. Clique no botão **`Save`** (embaixo, ao lado de `Add` e `Remove`)
11. Confira que a linha da tabela agora mostra os valores novos
12. **Feche a janela**

### Passo C — fazer os PCs da LAN3 pegarem IP automático

13. **Clique duas vezes** em `PC9` → aba **`Desktop`** → **`IP Configuration`**
14. Marque a bolinha **`DHCP`**
15. Espere 2 segundos → deve aparecer `DHCP request successful` e o IP `10.0.0.10` (ou próximo)
16. Feche e **repita nos `PC10`, `PC11` e `PC12`**

> Se aparecer `DHCP failed`, volte no `SRV-DHCP` e confira se o `Service` está em `On`
> e se você clicou em `Save`.

## 8.2 SRV-DNS (o da LAN1)

### Passo A — IP fixo

1. **Clique duas vezes** em `SRV-DNS` → aba **`Desktop`** → **`IP Configuration`**
2. Marque **`Static`**:
```
IPv4 Address    : 120.20.30.130
Subnet Mask     : 255.255.255.192
Default Gateway : 120.20.30.129
DNS Server      : 120.20.30.130
```

### Passo B — ligar o serviço DNS

3. Aba **`Services`** (no topo da janela)
4. Lista da esquerda → clique em **`DNS`**
5. Em `DNS Service`, marque **`On`**
6. Agora cadastre os registros. Para **cada um** da tabela abaixo:
   - No campo **`Name`** digite o nome
   - No dropdown **`Type`** escolha **`A Record`**
   - No campo **`Address`** digite o IP
   - Clique no botão **`Add`**

| Name | Type | Address |
|---|---|---|
| `index.html` | A Record | `120.20.30.131` |
| `www.projeto.com` | A Record | `120.20.30.131` |
| `servidor.eld310.com` | A Record | `120.20.30.131` |

7. Confira que os 3 aparecem na tabela de baixo
8. Feche a janela

> O registro **`index.html`** é o que o professor pediu no item 4.3.2 — é o nome que
> você vai digitar no navegador dos PCs.

## 8.3 SRV-HTTP (o da LAN1)

### Passo A — IP fixo

1. **Clique duas vezes** em `SRV-HTTP` → aba **`Desktop`** → **`IP Configuration`**
2. Marque **`Static`**:
```
IPv4 Address    : 120.20.30.131
Subnet Mask     : 255.255.255.192
Default Gateway : 120.20.30.129
DNS Server      : 120.20.30.130
```

### Passo B — ligar o serviço HTTP e editar a página

3. Aba **`Services`** → lista da esquerda → clique em **`HTTP`**
4. Em `HTTP`, marque **`On`**
5. Em `HTTPS`, marque **`On`**
6. Na tabela `File Manager` (embaixo), ache a linha do arquivo **`index.html`**
7. Na coluna da direita dessa linha, clique no link **`(edit)`**
8. Abre uma área de texto com o HTML. **Apague tudo** e cole isto:

```html
<html>
  <head><title>ELD310 - Projeto de Redes</title></head>
  <body>
    <center>
      <h1>Projeto de Interconexao de Redes e Servicos</h1>
      <h2>ELD310 - Laboratorio de Redes</h2>
      <hr>
      <p>Servidor HTTP funcionando com sucesso!</p>
      <p>Aluno: Igor Pieralini</p>
    </center>
  </body>
</html>
```

9. Clique no botão **`Save`** (canto inferior direito da área de texto)
10. Feche a janela

---

# PARTE 9 — CONFIGURANDO O WI-FI (Access Point + Laptops)

## 9.1 Configurar o Access Point

1. **Clique duas vezes** no `AP-LAN3`
2. Clique na aba **`Config`** (no topo)
3. Na **lista da esquerda**, em `INTERFACE`, clique em **`Port 1`**
   - ⚠️ É a **Port 1**, não a Port 0. A Port 0 é a porta de cabo, a Port 1 é o rádio Wi-Fi
4. Preencha/selecione:
```
SSID            : LAN3-WIFI
Authentication  : marque a bolinha WPA2-PSK
PSK Pass Phrase : redes2026
Encryption Type : AES
```
5. Feche a janela

> A senha `redes2026` tem 9 caracteres (WPA2 exige no mínimo 8). **Anote**, os laptops
> precisam da senha idêntica.

## 9.2 Trocar a placa de rede dos laptops (obrigatório!)

> O laptop vem de fábrica com placa **cabeada**. Sem trocar por uma placa Wi-Fi,
> ele nunca vai enxergar o Access Point.

### Faça isto no `Laptop1`:

1. **Clique duas vezes** no `Laptop1`
2. Clique na aba **`Physical`**
3. Na foto do laptop (lado direito), ache o **botão redondo de liga/desliga**
   — fica na **lateral direita** do desenho do laptop
4. **Clique nele** → o laptop desliga
5. Ainda na foto, ache o **módulo já instalado** no slot da lateral esquerda
   (é uma placa com um conector RJ-45, nome `PT-LAPTOP-NM-1CFE`)
6. **Arraste esse módulo para fora**, soltando em cima da lista `MODULES` da esquerda
   → o slot fica vazio
7. Na lista `MODULES` da esquerda, role até achar **`WPC300N`**
   - A descrição diz: *"...provides one 2.4GHz wireless interface suitable for connection to wireless networks"*
8. **Arraste** o `WPC300N` da lista para o **slot vazio** do laptop
9. **Clique** no botão de liga/desliga → o laptop liga
10. **Não feche a janela ainda**

### Ainda no Laptop1 — conectar no Wi-Fi:

11. Clique na aba **`Config`** (no topo)
12. Na lista da esquerda, em `INTERFACE`, clique em **`Wireless0`**
13. Preencha:
```
SSID            : LAN3-WIFI
Authentication  : marque a bolinha WPA2-PSK
PSK Pass Phrase : redes2026
Encryption Type : AES
```
14. Logo abaixo, em `IP Configuration`, marque a bolinha **`DHCP`**
15. Feche a janela
16. Um **link tracejado** deve aparecer entre o `Laptop1` e o `AP-LAN3`

### Repita TUDO (passos 1 a 16) no `Laptop2` e no `Laptop3`

---

# PARTE 10 — TESTES (item 4.3 do enunciado)

## 10.1 Conferir se o RIP funcionou

1. **Clique duas vezes** no `R1` → aba **`CLI`**
2. Digite e aperte Enter:
```
show ip route
```
3. Você deve ver várias linhas começando com **`R`** (de RIP), tipo:
```
R    10.0.0.0/18 [120/2] via 172.16.1.2, 00:00:15, Serial0/3/0
R    192.168.0.0/18 [120/1] via 172.16.1.2, 00:00:15, Serial0/3/0
R    192.168.64.0/18 [120/1] via 172.16.1.2, 00:00:15, Serial0/3/0
```
4. Se **não** aparecer nenhuma linha com `R`, **espere 60 segundos** e rode de novo
   (o RIP troca informações a cada 30 s)
5. Repita nos roteadores `R2` e `R3`

## 10.2 Testes de PING

**Como fazer um ping:**
1. **Clique duas vezes** no PC de origem
2. Aba **`Desktop`** → ícone **`Command Prompt`**
3. Digite o comando e aperte Enter

> ⚠️ **O primeiro ping quase sempre perde os 1º e 2º pacotes** (é o ARP resolvendo).
> Isso é NORMAL. Rode o comando **duas vezes** antes de achar que deu errado.

| # | Vá no PC | Digite | Resultado esperado |
|---|---|---|---|
| 1 | `PC1` | `ping 120.20.30.12` | ✅ Reply (mesma sub-rede) |
| 2 | `PC1` | `ping 120.20.30.131` | ✅ Reply (sub-rede 02, mesmo roteador) |
| 3 | `PC1` | `ping 192.168.0.10` | ✅ Reply (LAN1 → LAN2) |
| 4 | `PC5` | `ping 192.168.64.10` | ✅ Reply (LAN2 A → LAN2 B) |
| 5 | `PC5` | `ping 10.0.0.10` | ✅ Reply (LAN2 → LAN3) |
| 6 | `PC9` | `ping 120.20.30.10` | ✅ Reply (**LAN3 → LAN1 é PERMITIDO**) |
| 7 | `PC9` | `ping 192.168.0.10` | ❌ **Destination host unreachable** (ACL bloqueou 👍) |
| 8 | `Laptop1` | `ping 120.20.30.10` | ✅ Reply (Wi-Fi funcionando) |
| 9 | `Laptop1` | `ping 192.168.0.10` | ❌ Bloqueado pela ACL 👍 |

> O teste **7** e o **9** darem erro é **o resultado CORRETO** — significa que a ACL
> está funcionando. Tire print disso pro relatório.

## 10.3 Conferir o DHCP

1. **Clique duas vezes** no `PC9` → aba **`Desktop`** → **`Command Prompt`**
2. Digite:
```
ipconfig /all
```
3. Deve mostrar:
```
IPv4 Address....: 10.0.0.10
Subnet Mask.....: 255.255.192.0
Default Gateway.: 10.0.0.1
DNS Server......: 120.20.30.130
DHCP Servers....: 10.0.0.2
```

## 10.4 Testar o site (item 4.3.2) — TEM QUE FUNCIONAR NAS 3 LANs

**No PC1 (LAN1):**
1. Clique duas vezes no `PC1` → aba **`Desktop`**
2. Clique no ícone **`Web Browser`**
3. No campo `URL`, digite: **`index.html`**
4. Clique no botão **`Go`**
5. A página com o título *"Projeto de Interconexao de Redes e Servicos"* deve aparecer

**Repita exatamente isso no `PC5` (LAN2) e no `PC9` (LAN3).**

Nos 3 casos a página tem que abrir.

## 10.5 Conferir a ACL

1. Clique duas vezes no `R3` → aba **`CLI`**
2. Digite:
```
show access-lists
```
3. Deve mostrar a contagem de pacotes bloqueados:
```
Extended IP access list BLOQUEIA-LAN2
    10 deny ip 10.0.0.0 0.0.63.255 192.168.0.0 0.0.255.255 (8 match(es))
    20 permit ip any any (45 match(es))
```
4. O número de `match(es)` na linha `deny` prova que a ACL está bloqueando.
   **Tire print disso.**

---

# PARTE 11 — SALVAR E ENTREGAR

## 11.1 Salvar o arquivo .pkt

1. Menu superior → **`File`** → **`Save As...`**
2. Escolha a pasta onde quer salvar
3. Nome do arquivo:
```
ELD310_Projeto_Interconexao_Redes.pkt
```
4. Clique em **`Save`**

## 11.2 Tirar os prints para o PDF

Tire print (tecla `Print Screen` ou `Shift+Ctrl+PrtSc` no Ubuntu) de:
- [ ] A topologia inteira montada (com todos os links verdes)
- [ ] O `show ip route` do R1 mostrando as rotas `R`
- [ ] O ping do PC1 → PC5 com sucesso
- [ ] O ping do PC9 → PC5 **falhando** (prova da ACL)
- [ ] O `show access-lists` do R3 com os matches
- [ ] O `ipconfig /all` do PC9 mostrando IP via DHCP
- [ ] A página `index.html` aberta no navegador do PC1, PC5 e PC9
- [ ] A tela de configuração WPA2-PSK do Access Point

## 11.3 Montar o PDF

Junte num documento (Word/Google Docs → exportar PDF):
1. Capa com nome dos alunos
2. As tabelas de sub-redes da **Parte 1** (máscara, prefixo, faixa de IPs, broadcast)
3. A tabela mestre de IPs (seção 1.5)
4. Todos os prints da seção 11.2
5. Salve como `ELD310_Projeto_Enderecamento.pdf`

## 11.4 Upload no Moodle (prazo: 15/11)
- `ELD310_Projeto_Interconexao_Redes.pkt`
- `ELD310_Projeto_Enderecamento.pdf`

---

# CHECKLIST FINAL

- [ ] 3 roteadores 2911 com módulo HWIC-2T instalado
- [ ] 7 switches 2960 posicionados
- [ ] 12 PCs + 3 laptops + 3 servidores + 1 access point
- [ ] Todos os cabos ligados (23 cabos de cobre + 2 seriais)
- [ ] Todos os links **verdes** (nenhum vermelho)
- [ ] R1, R2 e R3 configurados com IP + RIP v2 + `no auto-summary`
- [ ] ACL no R3 bloqueando LAN3 → LAN2
- [ ] PC1 a PC8 com IP manual
- [ ] SRV-DHCP com serviço `On` e pool salvo
- [ ] PC9 a PC12 pegando IP por DHCP
- [ ] SRV-DNS com o registro `index.html`
- [ ] SRV-HTTP com a página editada
- [ ] AP com WPA2-PSK e senha `redes2026`
- [ ] 3 laptops com módulo `WPC300N` conectados no Wi-Fi
- [ ] Todos os pings da tabela 10.2 testados
- [ ] `index.html` abrindo no navegador do PC1, PC5 e PC9
- [ ] Arquivo `.pkt` salvo
- [ ] PDF montado com os prints

---

# DEU ERRO? PROCURE AQUI

| O que aconteceu | Por quê | Como resolver |
|---|---|---|
| Cabo serial **vermelho** | Falta `clock rate` no lado DCE | No roteador que você clicou PRIMEIRO: `interface Serial0/3/0` → `clock rate 64000` → `no shutdown` |
| Cabo **vermelho** entre 2 switches | Usou cabo reto | Delete o cabo (ferramenta ❌ na barra direita) e refaça com **Copper Cross-Over** |
| Link com **triângulo laranja** | Spanning-Tree calculando | **Espere 30 segundos**, vira verde sozinho |
| Não consigo arrastar o módulo | Equipamento está **ligado** | Aba `Physical` → clique no botão liga/desliga → arraste → ligue de novo |
| `% Invalid input detected` no CLI | Digitou no modo errado | Digite `enable` e depois `configure terminal` antes dos comandos |
| Ping entre LANs falha | RIP não convergiu | Espere 60 s; depois `show ip route` e veja se há linhas com `R` |
| RIP não passa as redes `/18` e `/25` | Falta `no auto-summary` | No CLI: `router rip` → `no auto-summary` |
| PC não pega IP no DHCP | Serviço off ou pool não salvo | `SRV-DHCP` → `Services` → `DHCP` → `On` → clique em **`Save`** |
| Navegador diz `Host Name Unresolved` | DNS errado no PC | `IP Configuration` → campo `DNS Server` = `120.20.30.130` |
| Navegador diz `Request Timeout` | Sem rota até o servidor | Faça `ping 120.20.30.131` primeiro; se falhar, o problema é o RIP |
| Laptop não acha o Wi-Fi | Ainda está com a placa de cabo | Aba `Physical` → desligue → troque o módulo por **`WPC300N`** → ligue |
| Laptop acha o Wi-Fi mas não conecta | Senha diferente | A senha do AP e do laptop tem que ser **idêntica**: `redes2026` |
| ACL bloqueou tudo | Faltou `permit ip any any` | Toda ACL tem um `deny all` invisível no final — o `permit` no fim é obrigatório |
| Interface `administratively down` | Faltou `no shutdown` | Entre na interface e digite `no shutdown` |
| Perdi tudo ao fechar | Não salvou | `File` → `Save`. Salve a cada 10 minutos! |