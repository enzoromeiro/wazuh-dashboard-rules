# Wazuh SIEM: Auditoria de Comandos e Correlação de Identidade
Este projeto demonstra a implementação de um ambiente de monitoramento centralizado utilizando o Wazuh, focado em visibilidade de terminal e auditoria de autenticação. O objetivo principal é transformar logs brutos em inteligência para um SOC (Security Operations Center).

### -Funcionalidades Implementadas
Auditoria Avançada (Linux):

Integração com auditd no Linux para capturar a execução de comandos sensíveis (ex: adduser, chmod, chown, whoami, etc).

### -Detecção de Ameaças em Tempo Real:

Criação de regras customizadas (.xml) para alertar sobre comandos de reconhecimento de rede, criação e remoção de usuário, alteração de permissão de arquivos, etc.

### -Dashboards de Correlação:

Painel centralizado correlacionando Quem (Usuário), Onde (Agente) e O que (Comando digitado).

Visualização de falhas de autenticação vs. logins bem-sucedidos para identificação de ataques de Brute Force.

### -Exemplo de Fluxo de Detecção
Evento 1: Um IP tenta se autenticar via ssh múltiplas vezes em um período de tempo espaçado, para não ser detectado por regras de brute force.

Evento 2: O IP consegue um login bem sucedido via ssh sem gerar alertas.

Evento 3: Um comando sensível é digitado logo após a autenticação.

Visualização: O evento aparece instantaneamente na Timeline de Comandos Sensíveis do Dashboard e a correlação fica nítida.

### -Tecnologias Utilizadas
Wazuh SIEM 

Máquinas virtuais: Linux (Ubuntu) & Windows 10 (atacante)

Putty

Auditd

Regras XML Customizadas

### Como Replicar
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

