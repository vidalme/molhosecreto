agent:
  name: string
  type: core | support | specialized

  role: string

  responsibilities:
    - string

  triggers:
    - string

  inputs:
    - string

  outputs:
    - string

  decision_logic:
    - condition: string
      action: string

  interactions:
    calls:
      - agent_name
    called_by:
      - agent_name

  rules:
    - string

  evolution:
    updates:
      - condition: string
        action: string

  constraints:
    - no duplication
    - modular design
    - clear IO
