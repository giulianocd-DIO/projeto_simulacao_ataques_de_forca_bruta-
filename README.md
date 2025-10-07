# Projeto de Simulação de Ataques de Força Bruta com Kali Linux e Medusa

## Descrição do Projeto
Este projeto tem como objetivo demonstrar a execução de ataques de força bruta utilizando Kali Linux e a ferramenta Medusa contra serviços vulneráveis do Metasploitable 2 e aplicação DVWA (Damn Vulnerable Web Application). O projeto também aborda a configuração do ambiente para testes de segurança, tipos de ataques cibernéticos, vulnerabilidades exploradas e medidas de mitigação para proteger sistemas.

## Objetivos
- Compreender ataques de força bruta em serviços FTP, Web e SMB.
- Utilizar Kali Linux e Medusa para auditoria de segurança em ambiente controlado.
- Documentar processos técnicos de forma clara e estruturada.
- Reconhecer vulnerabilidades comuns e propor medidas de mitigação.
- Disponibilizar a documentação e evidências em repositório GitHub como portfólio técnico.

## Ambiente Configurado
- Duas VMs no VirtualBox:
  - Kali Linux (atacante)
  - Metasploitable 2 (vítima vulnerável)
- Rede configurada como Host-Only para comunicação interna entre as VMs.
- DVWA instalado na Metasploitable 2 para testes em aplicações web.

## Ataques Executados

### 1. Ataque de Força Bruta FTP com Medusa
Comando:
medusa -h [IP_VITIMA] -u ftp -P wordlists/simple.txt -M ftp
 
