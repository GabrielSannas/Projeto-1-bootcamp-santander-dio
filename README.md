# Projeto-1-bootcamp-santander-dio
Documentação do Projeto — Simulação de Ataques de Força Bruta (Kali + Medusa + Ambientes Vulneráveis)

Resumo: documento detalhado para implementação, testes e registro de atividades de um laboratório de segurança que simula ataques de força bruta contra serviços (ex.: FTP, formulário web — DVWA, SMB) usando Kali Linux e a ferramenta Medusa, com Metasploitable 2 e DVWA como alvos. O foco é pedagógico: entender vetores de ataque, registrar evidências e propor medidas de mitigação defensivas.

1. Avisos éticos e legais

Somente execute estes testes em ambientes que você possui ou para os quais tem autorização explícita (por exemplo: VMs locais criadas para laboratório).

O objetivo é educacional: aprender vulnerabilidades, como mitigá-las e como detectar tentativas de ataque.

Preserve logs e evidências; use dados sintéticos (não exponha credenciais reais).

2. Objetivos do projeto

Montar um ambiente isolado com duas VMs (Kali e alvo vulnerável) em VirtualBox usando rede interna/host-only.

Simular ataques de força bruta com Medusa em diferentes vetores: FTP, formulário web (DVWA) e SMB (password spraying).

Registrar procedimentos, ferramentas, wordlists simples, resultados e validação de acessos.

Recomendar medidas de mitigação e detecção (defesa).

3. Arquitetura do laboratório (visão geral)

VM 1 (Atacante): Kali Linux (contendo Medusa, ferramentas de captura de tráfego, cronógrafos, etc.)

VM 2 (Alvo): Metasploitable 2 e/ou DVWA (podem ser na mesma VM ou VM separadas para serviços distintos).

Rede: VirtualBox — host-only ou rede interna (isolamento da rede real).

Ferramentas auxiliares: Wireshark/tcpdump para captura, Burp Suite (proxy) para analisar formulários web, editores de texto para documentação, sistema de controle de versão (Git) para rastrear resultados.

4. Plano de testes (cenários)

Cada cenário contém: objetivo, abordagem/fluxo, dados de entrada (wordlists conceituais), observáveis e métricas.

Cenário A — Força bruta em FTP

Objetivo: testar resistência de contas FTP a tentativas de adivinhação de senha.

Abordagem: usar uma ferramenta de brute-force para tentar combinações de usuário/senha contra o serviço FTP do alvo.

Wordlists: criar listas simples (ex.: variações de senhas comuns, combinações com o nome do usuário) — não use listas com dados reais.

Observáveis: número de tentativas por minuto, resposta do servidor (bloqueios, resets), logs de autenticação do alvo, se login bem-sucedido foi obtido.

Métricas: tempo até sucesso (se houver), taxa de tentativas, número de IPs/endereços bloqueados.

Cenário B — Automação de tentativas em formulário web (DVWA)

Objetivo: demonstrar ataques de força bruta/credential stuffing em um formulário de login web (DVWA configurada no modo adequado para testes).

Abordagem: automatizar submissões de formulário usando um proxy (Burp Suite, script) para iterar credenciais; registrar respostas do servidor e bloqueios.

Observáveis: comportamento do login (mensagens de erro, tempo de resposta, bloqueio por IP), logs de aplicação web, capturas HTTP(S).

Métricas: taxa de sucesso, número de requisições por segundo antes de detecção, eventuais triggers de WAF/IPS.

Cenário C — Password spraying em SMB com enumeração de usuários

Objetivo: testar estratégia de password spraying (usar poucas senhas contra muitos usuários para evitar bloqueios) em SMB; demonstrar importância de políticas de senha e bloqueio.

Abordagem: primeiro enumerar nomes de usuários válidos (apenas em ambiente de teste) e depois tentar um pequeno conjunto de senhas populares contra cada usuário, com intervalo entre tentativas para simular spraying.

Observáveis: respostas SMB, bloqueios de conta, geração de alertas em logs do controlador/serviço SMB.

Métricas: quantos usuários comprometidos, quantas tentativas necessárias, respostas do serviço a tráfego de spraying.

5. Procedimentos e boas práticas de documentação (como registrar)

Ficha do experimento: data, hora, versão das VMs, configurações de rede, snapshot do estado antes do teste.

Lista de ferramentas e versões (ex.: Kali 20XX.x, Medusa X.Y) — registre o source das ferramentas instaladas.

Wordlists: nome, tamanho, critérios de geração (por ex.: combinações, regras de mutação), amostra reduzida (não publicar listas completas se sensíveis).

Comandos / Padrões de execução: registre o modelo usado (pseudocódigo) e a saída relevante sanitized (senhas reais removidas).

Evidências: capturas de tela das telas com sucesso, trechos de logs, captures pcap relevantes (sem dados sensíveis), tempos e métricas.

Conclusões e recomendações: o que funcionou, o que não funcionou, riscos identificados e medidas sugeridas.

Dica: versionar a documentação (Git) e armazenar resultados e evidências em pasta protegida; inclua snapshots das VMs pré e pós-teste para reprodutibilidade.

6. Modelos (pseudocódigo/estrutura) — sem comandos executáveis detalhados

Vou mostrar estruturas de como você pode descrever execuções no relatório, sem fornecer comandos passo-a-passo que possam ser reutilizados fora do laboratório.

Exemplo de registro de execução (estrutura)

Alvo: IP A.B.C.D (VM: Metasploitable2)

Ferramenta: medusa (versão X) — descrição: ferramenta de força bruta multi-thread para serviços remotos.

Modo de uso (modelo):

medusa [parâmetros gerais: alvo, módulo (ex.: ftp/smb), lista de usuários, lista de senhas, opções de paralelismo/log]


No relatório registre: flags usadas (nomeadas), número de threads, duração, e qualquer ajuste feito (taxa por segundo, delays).

Resultado observado: ex.: "Login bem-sucedido para usuário teste após N tentativas; servidor retornou código Y; acesso ao diretório /home/teste possível." (sanitizar credenciais)

Exemplo para formulário web (estrutura)

Alvo: DVWA em http://A.B.C.D/dvwa

Abordagem: automação via proxy (captura req/res) ou script que envia POSTs ao endpoint de login.

Registro: endpoint alvo, parâmetros do formulário enviados, resposta HTTP observada para sucesso e falha, delays aplicados.

Resultado observado: "Requisições por segundo > X causaram erro 429; após ajuste para uma tentativa a cada Y segundos, não houve bloqueio."

7. Construção de wordlists (orientações seguras)

Comece pequeno: palavras-chave relacionadas ao contexto do laboratório (variantes do nome de usuário + números).

Use regras de mutação: prefixos/sufixos comuns, variações de capitalização, data fictícia, mas não publique wordlists que contenham dados sensíveis.

Ferramentas úteis (conceito): geradores de wordlist, scripts para combinar listas — documente o método e mostre amostras reduzidas (10–20 itens) no relatório.

8. Registro de evidências — o que salvar

Trechos de logs (sanitizados).

Capturas de tela (com timestamps).

Arquivos pcap (filtrados para remover dados sensíveis).

Hashes de arquivos e snapshots da VM.

Relatório de métricas: número de requisições, duração do teste, taxa de sucesso.

9. Análise de riscos e recomendações de mitigação (por serviço)

Apresento recomendações práticas e defensivas — foco em proteção e detecção.

Para FTP

Habilitar autenticação forte (senhas complexas, idealmente autenticação baseada em chave ou uso de SFTP/FTPS).

Bloqueio temporário após N tentativas falhas (throttling) e alerta (SIEM).

Limitar e monitorar acessos por IP; aplicar rate limiting e listas de bloqueio automatizadas.

Desabilitar contas padrões e políticas de senha mínima e rotação.

Para aplicações web (formularios)

Implementar limites de taxa por IP/usuário, CAPTCHAs após tentativas repetidas e lockout progressivo.

Usar mecanismos anti-bot (WAF) e pattern-matching para detectar credential stuffing.

Logar tentativas de autenticação e analisar padrões com sistema de correlação (SIEM).

Forçar MFA (autenticação multifator) para contas sensíveis.

Para SMB / Password spraying

Políticas de bloqueio e detecção de spray (monitorar tentativas distribuídas em contas distintas).

Requerer senhas fortes, proibir senhas pré-definidas e implementar MFA quando possível.

Monitorar logs de autenticação no controlador de domínio; detectar padrões de spraying (muitas contas com poucas senhas repetidas).

Desabilitar contas inativas e aplicar segmentação de rede.

10. Exemplo de sumário executivo (para apresentação)

Contexto: laboratório com Kali + Metasploitable/DVWA em VirtualBox.

Atividades: simulações de força bruta em FTP, automação em formulário web, e password spraying em SMB.

Achados principais: (resuma sem divulgar credenciais) ex.: “Força bruta simples com wordlists pequenas conseguiu comprovar falhas de políticas de senha em ambiente padrão.”

Recomendações chave: aplicar bloqueio de tentativas, MFA, hardening dos serviços e monitoramento centralizado.

11. Apêndice — checklist antes de entregar/compartilhar documentação

Remover ou anonimizar quaisquer credenciais reais.

Incluir instruções de como reproduzir o experimento apenas em ambiente controlado (usar snapshots, rede isolada).

Incluir referências bibliográficas/links de documentação das ferramentas (man pages, guias oficiais).

Incluir um glossário com termos técnicos (brute-force, password spraying, WAF, SIEM).

12. Modelo de sumário técnico (template pronto para preencher)

Título do experimento

Autor / Data

Objetivo

Ambiente (VMs, IPs, snapshots)

Ferramentas e versões

Wordlists (descrição)

Procedimento (estrutura)

Resultados (evidências)

Análise e impacto

Mitigações recomendadas

Conclusão

Anexos (logs, capturas)
