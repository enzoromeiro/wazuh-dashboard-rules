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
