# Playbook Operacional e Autonomia (Fase 4)

## Nível de Autonomia
- **Modo Híbrido**: O agente envia candidaturas automaticamente se todos os campos possuírem correspondência direta e segura com o perfil.
- **Pausa Humana Obrigatória (`RULE-001`)**: Qualquer presença de 2FA/SMS, CAPTCHA, pergunta eliminatória com dúvida ou criação forçada de conta externa interrompe o envio e move a vaga para a coluna `Pendentes` no Kanban Memorycard.

## Delays e Comportamento Orgânico
- Manter intervalos de 1 a 3 minutos entre candidaturas consecutivas.
- Respeitar o teto máximo de 20 candidaturas por dia no LinkedIn para evitar sinalização de atividade suspeita (*shadowban*).
