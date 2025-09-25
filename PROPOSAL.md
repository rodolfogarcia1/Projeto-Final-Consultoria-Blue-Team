# LojaZeta – Estratégia de Defesa em Camadas, Monitoramento e Resposta a Incidentes
**Elaborado por:** Rodolfo de Araujo Garcia  
**Data:** [2025-09-25]  
**Módulo:** Formação em CyberSEC – Módulo 2 (Defensa & Monitoramento)

---

## Capa
**Cliente:** LojaZeta (E-commerce)  
**Consultor:** Rodolfo de Araujo Garcia  
**Escopo:** Defesa em Camadas, Plano de Monitoramento/SIEM e Estrutura de Resposta a Incidentes

---

## Resumo Executivo
A LojaZeta é uma plataforma de e-commerce em rápido crescimento, baseada em uma infraestrutura na nuvem com uso de Nginx, Node.js e PostgreSQL. Devido à sua crescente visibilidade e exposição pública, a empresa se tornou alvo de ataques cibernéticos comuns, como injeção de SQL (SQLi), cross-site scripting (XSS) e tentativas de login por força bruta.

**Desafios atuais incluem:**
- **Ausência de monitoramento centralizado** →  logs espalhados em múltiplos servidores.
- **Recursos limitados** → equipe de TI pequena e restrições orçamentárias.
- **Backups não validados** → processos de backup existem, mas nunca foram testados com restauração.

Esta proposta recomenda uma **arquitetura de defesa em camadas**, **monitoramento centralizado com um SIEM viável** e um **plano de resposta à incidentes do NIST.**. A estratégia prioriza **vitórias rápidas nos primeiros 30 dias**, com grande impacto e uso mínimo de recursos (princípio 80/20), além de um **roteiro claro de 3, 6, 12 meses** para evolução da maturidade de Segurança.

**Principais recomendações incluem:**
- Implantar um **Web Application Firewall (WAF)** com ModSecurity/OWASP CRS para detectar e bloquear SQLi/XSS.
- Implementar uma solução de **SIEM leve** (Wazuh com ELK) para centralizar logs, gerar alertas e ter visibilidade.
- Fortalecer a **Segurança de Identidade** com autenticação multifator (MFA) e acesso por menor privilégio.
- Estabelecer uma **Política de Backup e Restauração testada** para garantir a resiliência de dados.
- Desenvolver **runbooks de resposta à incidentes**, permitindo que a equipe de TI atue de forma rápida e padronizada durante incidentes.

Com essas medidas, a LojaZeta reduzirá sua exposição a riscos, melhorará a capacidade de detecção e resposta, e construirá uma base de segurança escalável para seu crescimento futuro. Essa abordagem é econômica, prática e adequada ao porte e recursos da empresa.

---

## Objetivo
Projetar uma **estratégia de defesa em profundidade, plano de monitoramento e estrutura de resposta à incidentes** adaptados ao stack da LojaZeta (Nginx + Node.js + PostgreSQL) e às suas limitações de recursos.

---

## Escopo
- Arquitetura de defesa: perímetro → rede → host → aplicação → dados → identidade
- Plano de monitoramento/SIEM: centralização de logs, alertas, KPIs
- Resposta a Incidentes (ciclo de vida NIST)
- Roteiro: vitórias rápidas em 30 dias + estratégia de 3/6/12 meses

---

## Metodologia
1. Análise dos requisitos do briefing e riscos identificados.
2. Aplicação dos princípios de **Defesa em Camadas** e do **Framework de Cibersegurança do NIST**.
3. Seleção de tecnologias viáveis e com baixo custo (preferência por open-source).
4. Organização das recomendações por prioridade, utilizando o **princípio 30;20**.

---

## Arquitetura de Defesa em Camadas
`diagrama.png`

### Perímetro
- Firewall na nuvem com regras de entrada restritas.
- WAF com ModSecurity/OWASP CRS em Nginx.  
- Bloqueio geográfico e limitação de taxa para tráfego suspeito.

### Rede
- Segmentação de VPC: separar produção de desenvolvimento/testes.
- Implantação de IDS/IPS (Suricata ou agente Wazuh).

### Host
- Política de atualização de sistema operacional (atualizações mensais).
- Acesso SSH endurecido (autenticação por chave, usuários restritos).
- Prevenção de intrusões (fail2ban).

### Aplicação
- Boas práticas de codificação segura (queries parametrizadas).
- Escaneamento de dependências (`npm audit`, OWASP ZAP).
- Revisões de código com checagens de segurança.

### Dados
- Backups criptografados (AES-256).
- Testes de restauração de backup a cada trimestre.
- PostgreSQL reforçado (papéis com menor privilégio, conexões TLS).

### Identidade
- MFA para todas as contas administrativas.
- Controle de acesso baseado em papéis (RBAC) no IAM da nuvem.
- Princípio do menor privilégio aplicado.

---

## Plano de Monitoramento & SIEM
**Fontes de Log:**  
- Logs de acesso/erro do Nginx.
- Logs da aplicação Node.js. 
- Logs do PostgreSQL.
- Eventos do WAF/IDS.
- Logs de sistema (autenticação, eventos do SO).

**Regras de Correlação:**  
- Múltiplas falhas de login → detecção de força bruta.
- Assinaturas de SQLi/XSS → alertas do WAF.
- Tráfego de saída incomum → possível exfiltração de dados.

**KPIs e Métricas:**
- **MTTD (Tempo Médio para Detectar).**
- **MTTR (Tempo Médio para Responder).**
- **Taxa de falsos positivos.**
- **Número de incidentes graves por trimestre.**

**Ferramenta Proposta:**  
- **Wazuh (SIEM open-source)** com backend ELK para coleta centralizada de logs e geração de alertas.

---

## Plano de Resposta a Incidentes (Ciclo NIST)

### 1. Preparação
- Definir papéis da equipe de resposta (líder de operações, suporte de dev, contato da gestão).
- Estabelecer canal de comunicação seguro (Signal/Matrix/Slack dedicado).
- Pré-configurar alertas no SIEM e playbooks de resposta.

### 2. Detecção & Análise
- Monitorar alertas do WAF/IDS/SIEM.
- Correlacionar alertas com logs para confirmar positivos reais.
- Classificar incidentes por severidade (baixa/média/alta).

### 3. Contenção
- Isolar máquina afetada via grupos de segurança da nuvem.
- Bloquear IPs maliciosos no WAF/firewall.
- Desativar contas comprometidas.

### 4. Erradicação
- Aplicar patches em sistemas (app, SO, banco de dados).
- Remover malwares/backdoors.
- Redefinir credenciais comprometidas.

### 5. Recuperação
- Restaurar sistemas afetados a partir de backups verificados.
- Executar verificações de integridade pós-restauração.
- Validar funcionamento antes de voltar ao ar.

### 6. Lições Aprendidas
- Realizar análise pós-incidente.
- Atualizar runbooks e medidas de defesa.
- Treinar equipe com base nas melhorias detectadas.

---

## Recomendações (Prioridade 80/20)

### Em até 30 Dias (Vitórias Rápidas)
- Implantar WAF com ModSecurity em modo detecção → mudar para bloqueio após ajustes.
- Implementar logging centralizado com Wazuh/ELK.
- Aplicar MFA para contas administrativas.
- Realizar teste completo de restauração de backup.
- Configurar fail2ban para mitigar força bruta.

### Em até 3 Meses
- Ajustar regras do SIEM para reduzir falsos positivos.
- Desenvolver e documentar playbooks de IR (contenção, erradicação, recuperação).
- Isolar ambientes de desenvolvimento/teste da produção.

### Em até 6 Meses
- Implantar IDS/IPS (Suricata ou agentes Wazuh).
- Agendar varreduras de vulnerabilidades (OWASP ZAP, npm audit).
- Iniciar programa de conscientização em segurança para colaboradores.

### Em até 12 Meses
- Automatizar fluxos de IR com integrações do SIEM.
- Integrar feeds de inteligência de ameaças no SIEM.
- Realizar exercício Red Team / Blue Team para validar defesas.

---

## Roteiro (3/6/12 Meses)

| Prazo | Ações Estratégicas | Resultado Esperado |
|----------|------------------|------------------|
| **0–30 dias (Vitórias Rápidas)** | Implantar WAF, centralizar logs, aplicar MFA, testar backups, bloquear força bruta | Visibilidade imediata e redução de exposição a riscos |
| **3 meses** | Ajustar SIEM, criar playbooks de IR, segmentar ambientes | Alertas mais eficazes e resposta rápida e consistente |
| **6 meses** | Implantar IDS/IPS, iniciar varreduras de vulnerabilidades, treinamento de usuários | Detecção avançada e redução do risco de fatores humanos |
| **12 meses** | Automatize o IR, integre informações sobre ameaças, exercite a equipe vermelha/azul | Defesa adaptável e madura com caça proativa a ameaças |

---

## Riscos e Suposições
- **Riscos:**  
  - Recursos limitados de TI podem atrasar a implementação.
  - Restrições orçamentárias limitam as opções de ferramentas comerciais.
  - Os invasores podem aumentar a sofisticação mais rapidamente do que as defesas são implantadas.

- **Suposições:**  
  - O provedor de nuvem oferece suporte às melhores práticas de IAM e exportações de logs.
  - A equipe de desenvolvimento coopera na aplicação de práticas de codificação seguras.
  - A gerência oferece suporte à MFA e aos testes de restauração obrigatórios.

---

## Conclusão
Ao adotar essa estratégia de defesa em camadas, a LojaZeta aumentará significativamente sua resiliência contra ataques a aplicações web, reduzirá os tempos de detecção e resposta e estabelecerá um roteiro de segurança sustentável.
A combinação de **ganhos rápidos** e um **plano de longo prazo** garante que a LojaZeta possa proteger suas operações enquanto continua a crescer em um ambiente competitivo de comércio eletrônico.
---

## Attachments
- Diagrama de Arquitetura de Defesa `diagram.png`.
- Runbooks (`/runbooks/containment.md`, `/runbooks/eradication.md`, `/runbooks/recovery.md`).  
- Example log snippets `screenshot-wazuh.png`.
