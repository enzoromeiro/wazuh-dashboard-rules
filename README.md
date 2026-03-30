# 🛡️ Wazuh SIEM: Visibilidade de Terminal e Identidade
Este projeto foca em duas camadas críticas de segurança:

Quem acessou? (Auditoria de Autenticação: Logins e tentativas de invasão).

O que fez? (Auditoria de Comandos: Monitoramento via Auditd no Linux para capturar a execução de comandos sensíveis (ex: adduser, chmod, chown, whoami, etc)).

A união dessas camadas permite a Correlação de Identidade, transformando logs técnicos em informações claras através do dashboard customizado, auxiliando a tomada de decisão em um SOC.

### -Detecção de Ameaças em Tempo Real:

Criação de regras customizadas (.xml) para alertar sobre comandos de reconhecimento de rede, criação e remoção de usuário, alteração de permissão de arquivos, etc.

### -Dashboards de Correlação:

Painel centralizado correlacionando Quem (Usuário), Onde (Agente) e O que (Comando digitado).

Visualização de falhas de autenticação vs. logins bem-sucedidos para identificação de ataques de Brute Force.

### 🛠️ -Tecnologias Utilizadas
Wazuh SIEM 

Máquinas virtuais: Linux (Ubuntu) & Windows 10 (atacante)

Putty

Auditd

Regras XML Customizadas

### 📂 Como Replicar
Ambiente: Importe as VMs (Ubuntu para o Manager/Agente e Windows para o Atacante).

Agente Linux: Instale o auditd (sudo apt install auditd) crie um arquivo na pasta /etc/audit/rules.d/ e adicione as regras de monitoramento no arquivo criado por você, no meu caso eu nomeei de auditsoc.rules. Nessa pasta já vem um arquivo chamado "audit.rules" mas não adicione sua regra ali, pois é temporário. Por padrão eu utilizei as regras do Florian Roth (https://github.com/neo23x0/auditd), mas para esse projeto eu criei uma regra que monitora execução de arquivos, através da syscall execve.

#### Arquivo criado:
![audit rule path](/img/regrasaudit-path.jpeg)
#### Regra: 
![audit rule](/img/regraaudit.jpeg)

Configuração Wazuh: No arquivo ossec.conf do agente, habilite a leitura do log do audit: <location>/var/log/audit/audit.log</location>.

![ossec.config file](/img/ossecconfig-audit.jpeg)

Regras Customizadas: Copie os arquivos .xml da pasta /wrules deste repositório para o arquivo em /var/ossec/etc/rules/local_rules.xml no seu Manager. A regra 100004 eu criei para elíminar o ruído de um evento que estava ocorrendo frequentemente, mas era de level baixo (é opcional).

![regras locais wazuh](/img/regraslocais.jpeg)

### 📊 -Exemplo de Fluxo de Detecção

Evento 1: Um IP tenta se autenticar via ssh múltiplas vezes em um período de tempo espaçado, para não ser detectado por regras de brute force. Como pode ser observado no dashboard abaixo, em "IPs com mais erros de autenticação SSH", os top 5 IPs com mais erros serão mostrados ali, independentemente se foi um brute force agressivo ou um espaçado com o objetivo de bypassar as regras de detecção. E em "Tabela de IPs com erro de autenticação via SSH (origem e destino)" podemos observar a origem do IP que está tentando acessar e o usuário de destino.

![dashboard de erros](/img/dashboard-erros.png)

Evento 2: O IP consegue um login bem sucedido via ssh sem gerar alertas, mas é facilmente notado no dashboard que houve um login bem sucedido vindo do mesmo IP que teve mais erros de autenticação (192.168.1.16), que pode ser observado em "Timeline de autenticações bem sucedidas ssh" e "Tabela de IPs que logaram via SSH (origem e destino)". Ligando os pontos, até então, temos um IP que pode ser externo e suspeito ou no caso desse laboratório, um IP interno que cometeu ações suspeitas e realizou um login bem sucedido. 

![dashboard de login bem sucedido](/img/dashboard-login.png)

Evento 3: Um comando sensível é digitado logo após a autenticação, o dashboard abaixo mostra qual comando foi digitado e qual usuário digitou, como pode ser visualizado em "Timeline comandos sensíveis digitados por agentes" e "Tabela de comandos sensíveis digitados por agentes". Nessa etapa fica claro que o atacante após realizar o login, fez um reconhecimento na rede e tentou adicionar um novo usuário para manter uma persistência, o que classifica como um sinal vermelho de pós-exploração. 
A configuração do dashboard permite diversos filtros para facilitar a visualização desses eventos, pois diferente do laboratório, no cenário real existe bem mais ruído, IPs, máquinas sendo monitoradas etc.

![dashboard de comandos sensíveis executados](/img/dashboard-comandos.png)

#### Considerações finais: Não existe solução definitiva em segurança da informação, o mundo evolui, as metodologias usadas por atacantes evoluem, as ferramentas evoluem, no fim das contas cuidar dos três pilares (CID) da segurança da informação, é um trabalho constante e inevitável para todo negócio que almeja crescimento.
