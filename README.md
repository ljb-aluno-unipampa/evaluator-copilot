# Relatório Comparativo de Avaliação de Artefatos

## Identificação

**Nome do aluno:** LEAS. O nome foi inferido a partir do histórico Git dos três repositórios, que registra commits como `LEAS <aluno@unipampa.edu.br>`. As licenças dos artefatos também indicam `ljb-aluno-unipampa` como detentor de copyright.

**Título do artefato avaliado:** Laboratório Dockerizado de Gateway para DHCP, NAT e Firewall com Kea DHCP, nftables e API Flask.

**Problema de Cibersegurança abordado:** isolamento e controle de tráfego em redes locais virtuais containerizadas. Os artefatos simulam uma LAN interna, clientes DHCP, um gateway Linux com NAT e regras nftables, e uma API administrativa para observação ou alteração de estado. O problema central é reduzir exposição indevida entre redes, controlar encaminhamento LAN/WAN e tornar experimentos de segurança de rede reproduzíveis em ambiente Docker.

**Versões avaliadas:**

- [coder-deepseek](/home/leas/LEAS/coder-deepseek)
- [coder-gemini](/home/leas/LEAS/coder-gemini)
- [coder-gpt](/home/leas/LEAS/coder-gpt)

## Critérios e Metodologia

A avaliação foi conduzida com base nas instruções de avaliação de artefatos do SBSeg 2026: [Avaliação de Artefato - Instruções de submissão](https://doc-artefatos.github.io/sbseg2026/subinstrucoes.html). Foram considerados especialmente:

- requisitos mínimos esperados no README;
- selo de Artefatos Disponíveis;
- selo de Artefatos Funcionais;
- selo de Artefatos Sustentáveis;
- selo de Experimentos Reprodutíveis.

O procedimento de avaliação combinou:

- leitura dos READMEs e arquivos auxiliares;
- inspeção de Dockerfiles, Compose, scripts de inicialização e módulos Flask;
- validação estática com `docker compose config`;
- compilação Python com `py_compile`, usando cache temporário fora dos repositórios;
- smoke tests com Docker Compose, API, DHCP, rota dos clientes e ping externo;
- inspeção de segurança básica dos endpoints administrativos e das políticas nftables.

Os containers foram executados um repositório por vez porque os três artefatos usam nomes fixos como `client1`, `client2`, `gateway` ou `gw`.

## Resumo Executivo

| Repositório | Resultado geral | Melhor qualidade | Principal limitação | Indicação geral |
| --- | --- | --- | --- | --- |
| [coder-deepseek](/home/leas/LEAS/coder-deepseek) | Bom artefato funcional e coerente | Firewall base mais aderente ao problema de segurança | Baixa modularidade e ausência de automação de testes | Forte para demonstrar DHCP/NAT/firewall stateful |
| [coder-gemini](/home/leas/LEAS/coder-gemini) | Funcional, mas menos maduro como artefato científico | Dashboard e fluxo de reservas DHCP | README incompleto para SBSeg e implementação mais acoplada | Útil como protótipo interativo |
| [coder-gpt](/home/leas/LEAS/coder-gpt) | Melhor pacote científico geral, com ressalvas | Documentação, organização e reprodutibilidade | Falha estática em arquivo residual e firewall permissivo | Melhor base geral após correções imediatas |

**Conclusão comparativa:** [coder-gpt](/home/leas/LEAS/coder-gpt) apresenta a melhor qualidade geral como artefato científico, principalmente por documentação, modularidade, persistência e experimentos descritos. Entretanto, [coder-deepseek](/home/leas/LEAS/coder-deepseek) apresenta a política de firewall mais coerente com o problema de cibersegurança. [coder-gemini](/home/leas/LEAS/coder-gemini) funcionou nos testes, mas precisa de melhorias documentais e estruturais para se alinhar ao padrão SBSeg.

## Avaliação Individual: [coder-deepseek](/home/leas/LEAS/coder-deepseek)

### Visão Geral

[coder-deepseek](/home/leas/LEAS/coder-deepseek) implementa um laboratório enxuto com gateway Docker, Kea DHCP, NAT/firewall nftables e API Flask em um único módulo principal. O [README.md](/home/leas/LEAS/coder-deepseek/README.md:1) descreve a topologia, requisitos, dependências, segurança, instalação, teste mínimo, experimentos e licença. O núcleo da API está em [api/app.py](/home/leas/LEAS/coder-deepseek/api/app.py:1), o gateway é inicializado por [docker/gateway/entrypoint.sh](/home/leas/LEAS/coder-deepseek/docker/gateway/entrypoint.sh:1), e o firewall base está em [nftables/ruleset.nft](/home/leas/LEAS/coder-deepseek/nftables/ruleset.nft:1).

### Pontos Fortes

- O [README.md](/home/leas/LEAS/coder-deepseek/README.md:79) cobre bem requisitos de host, dependências, riscos de segurança, instalação, teste mínimo e reivindicações experimentais.
- O [docker-compose.yml](/home/leas/LEAS/coder-deepseek/docker-compose.yml:1) é simples e reprodutível, com rede LAN interna e WAN separada.
- A regra de firewall em [nftables/ruleset.nft](/home/leas/LEAS/coder-deepseek/nftables/ruleset.nft:36) usa política `drop` na cadeia `forward`, aceita tráfego estabelecido e permite LAN para WAN. Esta é a política mais alinhada ao problema de cibersegurança entre as três versões.
- A API em [api/app.py](/home/leas/LEAS/coder-deepseek/api/app.py:57) expõe status, leases e regras de firewall de forma direta.
- A separação de templates e regras em [kea](/home/leas/LEAS/coder-deepseek/kea) e [nftables](/home/leas/LEAS/coder-deepseek/nftables) facilita a inspeção por avaliadores.
- O smoke test executado confirmou funcionamento de API, DHCP, rota e NAT.

### Limitações

- A implementação Flask é concentrada em [api/app.py](/home/leas/LEAS/coder-deepseek/api/app.py:1), o que facilita leitura inicial, mas limita evolução e testes unitários.
- O repositório não possui testes automatizados reais. O arquivo [scripts/test_connectivity.sh](/home/leas/LEAS/coder-deepseek/scripts/test_connectivity.sh:1) está vazio.
- O [README.md](/home/leas/LEAS/coder-deepseek/README.md:8) informa que o título formal e o resumo do artigo não estão incluídos. Isso reduz aderência ao modelo mínimo SBSeg.
- A seção de selos considerados não aparece explicitamente no [README.md](/home/leas/LEAS/coder-deepseek/README.md:14).
- Não há CI, versionamento fixo de pacotes ou release arquivada.

### Problemas Encontrados

- A API aceita expressões nftables fornecidas pelo usuário em [api/app.py](/home/leas/LEAS/coder-deepseek/api/app.py:95). O uso de `shlex.split` e lista de argumentos reduz risco de shell injection, mas ainda permite que entradas semanticamente perigosas sejam executadas pelo `nft`.
- Endpoints de leitura, como [api/app.py](/home/leas/LEAS/coder-deepseek/api/app.py:76), são públicos. Isso é aceitável para laboratório, mas precisa ser explicitado como decisão de ameaça.
- O [docker-compose.yml](/home/leas/LEAS/coder-deepseek/docker-compose.yml:7) usa `privileged: true`, necessário para o experimento, mas de alto risco em host compartilhado.
- O repositório depende da interpretação do README para reproduzir experimentos; não há script automatizado de validação end-to-end.

### Melhorias Recomendadas

- Adicionar seção explícita de "Selos Considerados" no [README.md](/home/leas/LEAS/coder-deepseek/README.md:14).
- Preencher ou remover [scripts/test_connectivity.sh](/home/leas/LEAS/coder-deepseek/scripts/test_connectivity.sh:1), preferencialmente implementando smoke test automatizado.
- Validar semanticamente expressões nftables em [api/app.py](/home/leas/LEAS/coder-deepseek/api/app.py:95), por exemplo com lista permitida de ações/campos.
- Adicionar testes automatizados para `/api/status`, `/api/leases`, regra temporária de firewall e conectividade LAN/WAN.
- Documentar explicitamente os limites de autenticação dos endpoints de leitura.

### Evidências Observadas

- `docker compose config`: sucesso.
- `py_compile` de [api/app.py](/home/leas/LEAS/coder-deepseek/api/app.py:1): sucesso.
- `docker compose up --build -d`: sucesso.
- `docker compose ps`: `gateway`, `client1` e `client2` ficaram `Up`.
- `GET /api/status`: retornou `{"status":"running","interfaces":3}`.
- `GET /api/leases`: retornou lista com 2 leases.
- `client1`: recebeu `192.168.100.101/24` via DHCP e rota `default via 192.168.100.1`.
- `ping -c 1 8.8.8.8` a partir de `client1`: `0% packet loss`.
- `GET /api/firewall/rules`: retornou 2 regras na cadeia observada.

### Aderência aos Selos SBSeg

- **Disponível:** parcial. Há repositório local e licença, mas não foi observada referência a release estável/DOI.
- **Funcional:** bom. O artefato executou e demonstrou DHCP, API, NAT e firewall.
- **Sustentável:** razoável. Código é legível e simples, mas pouco modular e sem testes.
- **Reprodutível:** bom. O README descreve reivindicações, comandos, resultados e recursos esperados.

## Avaliação Individual: [coder-gemini](/home/leas/LEAS/coder-gemini)

### Visão Geral

[coder-gemini](/home/leas/LEAS/coder-gemini) implementa um gateway com Kea DHCP, Kea Control Agent, nftables, API Flask autenticada e dashboard web inline. O [README.md](/home/leas/LEAS/coder-gemini/README.md:1) foca arquitetura, pré-requisitos, instalação, validação de leases e interface. O backend é organizado em [gateway/gwapi_app](/home/leas/LEAS/coder-gemini/gateway/gwapi_app), com endpoints DHCP em [routes_dhcp.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/routes_dhcp.py:1), serviço DHCP em [dhcp_service.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/dhcp_service.py:1), firewall em [firewall.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/firewall.py:1) e UI em [routes_base.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/routes_base.py:1).

### Pontos Fortes

- O smoke test confirmou funcionamento de DHCP, Kea Control Agent, API autenticada, rota e NAT.
- A API DHCP em [routes_dhcp.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/routes_dhcp.py:9) exige HTTP Basic para status, leases, reservas e aplicação de configuração.
- O serviço [dhcp_service.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/dhcp_service.py:40) valida MAC, IP e hostname ao cadastrar reservas.
- O script [gateway/start-gateway.sh](/home/leas/LEAS/coder-gemini/gateway/start-gateway.sh:60) gera configuração mínima do Kea Control Agent alinhada ao socket do DHCP, permitindo consulta operacional via API.
- O [README.md](/home/leas/LEAS/coder-gemini/README.md:38) agora explicita requisitos de host, Docker, Compose, Git, recursos e dependências instaladas nos containers.
- O dashboard em [routes_base.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/routes_base.py:7) torna o artefato mais demonstrável para avaliadores humanos.

### Limitações

- O [README.md](/home/leas/LEAS/coder-gemini/README.md:1) não segue integralmente o modelo SBSeg: não há seções explícitas de estrutura do README, selos considerados, preocupações com segurança, experimento por reivindicação ou licença.
- A UI é um HTML inline grande em [routes_base.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/routes_base.py:7), o que dificulta manutenção, teste e revisão.
- O [docker-compose.yml](/home/leas/LEAS/coder-gemini/docker-compose.yml:21) monta todo o diretório `gateway` como `/app`, acoplando código-fonte e estado runtime.
- O script [gateway/start-gateway.sh](/home/leas/LEAS/coder-gemini/gateway/start-gateway.sh:5) aplica `chmod -R 777` em diretórios do Kea, solução prática mas frágil do ponto de vista de segurança.
- O código não possui testes automatizados nem CI.

### Problemas Encontrados

- [dhcp_service.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/dhcp_service.py:69) ignora exceções ao ler leases, podendo mascarar falhas de parsing ou permissão.
- [routes_dhcp.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/routes_dhcp.py:65) oferece um proxy de comandos ao Kea. Embora protegido por Basic Auth, ele amplia a superfície administrativa e deveria limitar comandos permitidos.
- [firewall.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/firewall.py:45) mantém política `accept` na cadeia `forward`, então a proteção de encaminhamento é menos forte que a de [coder-deepseek](/home/leas/LEAS/coder-deepseek).
- O [README.md](/home/leas/LEAS/coder-gemini/README.md:136) inclui credencial padrão em comando de exemplo. Isso é útil para laboratório, mas deve vir acompanhado de alerta de segurança mais explícito.

### Melhorias Recomendadas

- Reestruturar o [README.md](/home/leas/LEAS/coder-gemini/README.md:1) para cobrir todos os campos mínimos SBSeg, especialmente selos, segurança, experimentos por reivindicação e licença.
- Separar a UI de [routes_base.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/routes_base.py:7) em templates ou arquivos estáticos.
- Substituir `chmod -R 777` em [gateway/start-gateway.sh](/home/leas/LEAS/coder-gemini/gateway/start-gateway.sh:5) por permissões mínimas necessárias.
- Restringir comandos aceitos pelo proxy Kea em [routes_dhcp.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/routes_dhcp.py:65).
- Tornar o estado runtime, como reservas e leases, explicitamente persistido em volumes próprios.
- Adicionar smoke test automatizado para status do Kea, leases, reserva DHCP inválida/válida e aplicação de firewall.

### Evidências Observadas

- `docker compose config`: sucesso.
- `py_compile` de [gateway/gwapi.py](/home/leas/LEAS/coder-gemini/gateway/gwapi.py:1) e módulos em [gateway/gwapi_app](/home/leas/LEAS/coder-gemini/gateway/gwapi_app): sucesso.
- `docker compose up --build -d`: sucesso.
- `docker compose ps`: `gateway`, `client1` e `client2` ficaram `Up`.
- `GET /api/dhcp/status` autenticado: `{"result":0,"sockets":"ready"}`.
- `GET /api/dhcp/leases` autenticado: lista com 2 leases.
- `client1`: recebeu `192.168.222.51/24` e rota `default via 192.168.222.254`.
- `ping -c 1 8.8.8.8` a partir de `client1`: `0% packet loss`.
- `GET /api/dhcp/reservations` autenticado: lista vazia, endpoint funcional.

### Aderência aos Selos SBSeg

- **Disponível:** parcial. Há repositório local, licença e URL de clone no README, mas não foi observada release estável.
- **Funcional:** bom. O artefato executou e demonstrou funcionalidades centrais.
- **Sustentável:** parcial. Há separação de módulos, mas UI inline, permissões amplas e tratamento silencioso de erros prejudicam manutenção.
- **Reprodutível:** parcial. A instalação e teste mínimo existem, mas faltam reivindicações experimentais estruturadas no padrão SBSeg.

## Avaliação Individual: [coder-gpt](/home/leas/LEAS/coder-gpt)

### Visão Geral

[coder-gpt](/home/leas/LEAS/coder-gpt) é o artefato mais estruturado documentalmente. O [README.md](/home/leas/LEAS/coder-gpt/README.md:1) descreve título, resumo, organização, ambiente, dependências, segurança, instalação, teste mínimo, experimentos, endpoints, limpeza, limitações e licença. Também há documentos auxiliares como [ARTEFATO.md](/home/leas/LEAS/coder-gpt/ARTEFATO.md), [AVALIACAO-RAPIDA.md](/home/leas/LEAS/coder-gpt/AVALIACAO-RAPIDA.md) e [TOPOLOGIAS-DE-IMPLANTACAO.md](/home/leas/LEAS/coder-gpt/TOPOLOGIAS-DE-IMPLANTACAO.md). A API principal está em [gateway/gwapi_app/api.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/api.py:1), com factory em [gateway/gwapi_app/__init__.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/__init__.py:1) e rotas web protegidas por sessão em [gateway/gwapi_app/web.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/web.py:1).

### Pontos Fortes

- O [README.md](/home/leas/LEAS/coder-gpt/README.md:129) é o mais completo em requisitos de execução, dependências e ambiente.
- O [README.md](/home/leas/LEAS/coder-gpt/README.md:375) descreve experimentos por reivindicação, com comandos, recursos esperados e resultados.
- O [docker-compose.yml](/home/leas/LEAS/coder-gpt/docker-compose.yml:17) persiste dados e artefatos gerados em volumes bind para [gateway/data](/home/leas/LEAS/coder-gpt/gateway/data) e [gateway/generated](/home/leas/LEAS/coder-gpt/gateway/generated).
- A aplicação Flask usa factory em [gateway/gwapi_app/__init__.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/__init__.py:6), blueprints e templates separados.
- O [gateway/Dockerfile](/home/leas/LEAS/coder-gpt/gateway/Dockerfile:19) cria venv Python e instala dependências pip no container, deixando claro o limite entre host e runtime.
- O smoke test confirmou API, DHCP, leases, rota e NAT.

### Limitações

- O firewall gerado em [gateway/start-gateway.sh](/home/leas/LEAS/coder-gpt/gateway/start-gateway.sh:96) usa `policy accept` em `input`, `forward` e `output`, o que enfraquece a reivindicação de firewall como mecanismo de controle.
- Os endpoints REST de firewall em [gateway/gwapi_app/api.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/api.py:88) aceitam escrita sem autenticação, enquanto a autenticação aparece apenas nas páginas web de [gateway/gwapi_app/web.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/web.py:6).
- O endpoint de status DHCP depende do Kea Control Agent em [gateway/gwapi_app/api.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/api.py:37), mas o teste retornou `result: 1`.
- Não há testes automatizados nem CI.
- A seção "Selos Considerados" não aparece explicitamente no [README.md](/home/leas/LEAS/coder-gpt/README.md:13).

### Problemas Encontrados

- [gateway/gwapi_app/init.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/init.py:7) contém um bloco Markdown dentro de arquivo Python e falha com `IndentationError`. O fluxo principal não importa esse arquivo, mas ele prejudica qualidade, manutenção e validação estática do repositório.
- O template [gateway/templates/kea-ctrl-agent.conf.tpl](/home/leas/LEAS/coder-gpt/gateway/templates/kea-ctrl-agent.conf.tpl:8) aponta o Control Agent para `/run/kea/kea4-ctrl-socket`, enquanto [gateway/templates/kea-dhcp4.conf.tpl](/home/leas/LEAS/coder-gpt/gateway/templates/kea-dhcp4.conf.tpl:1) não define explicitamente um `control-socket`. Isso explica a resposta não saudável observada em `/api/dhcp/status`.
- Foi possível criar e remover regra de firewall sem autenticação via `POST /api/firewall` e `DELETE /api/firewall/<handle>`.
- O ruleset gerado observado em [gateway/generated/ruleset.nft](/home/leas/LEAS/coder-gpt/gateway/generated/ruleset.nft:12) usa `policy accept` na cadeia `forward`.

### Melhorias Recomendadas

- Corrigir ou remover [gateway/gwapi_app/init.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/init.py:1).
- Alinhar [gateway/templates/kea-ctrl-agent.conf.tpl](/home/leas/LEAS/coder-gpt/gateway/templates/kea-ctrl-agent.conf.tpl:8) e [gateway/templates/kea-dhcp4.conf.tpl](/home/leas/LEAS/coder-gpt/gateway/templates/kea-dhcp4.conf.tpl:1) para que o Control Agent consulte corretamente o DHCP4.
- Proteger endpoints de escrita em [gateway/gwapi_app/api.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/api.py:88) com autenticação ou token.
- Alterar a política base de firewall em [gateway/start-gateway.sh](/home/leas/LEAS/coder-gpt/gateway/start-gateway.sh:96) para um modelo stateful com `forward drop`, semelhante ao de [coder-deepseek](/home/leas/LEAS/coder-deepseek).
- Adicionar seção explícita de selos considerados ao [README.md](/home/leas/LEAS/coder-gpt/README.md:13).
- Criar teste automatizado que falhe se `/api/dhcp/status` retornar `result != 0`.

### Evidências Observadas

- `docker compose config`: sucesso.
- `py_compile`: falhou em [gateway/gwapi_app/init.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/init.py:7) com `IndentationError`.
- `docker compose up --build -d`: sucesso.
- `docker compose ps`: `gw`, `client1` e `client2` ficaram `Up`.
- `GET /api/health`: retornou `{"status":"ok"}`.
- `GET /api/dhcp/status`: retornou `result: 1`.
- `GET /api/dhcp/leases`: retornou lista com 2 leases.
- `client1`: recebeu `192.168.100.101/24` e rota `default via 192.168.100.254`.
- `ping -c 1 8.8.8.8` a partir de `client1`: `0% packet loss`.
- `POST /api/firewall` sem autenticação criou regra temporária com `handle: 4`; `DELETE /api/firewall/4` removeu a regra.

### Aderência aos Selos SBSeg

- **Disponível:** parcial. Há repositório local, licença e documentação ampla, mas não foi observada release estável/DOI.
- **Funcional:** bom com ressalvas. O fluxo principal executou, mas o endpoint de status DHCP via Control Agent não retornou sucesso.
- **Sustentável:** razoável a bom. A estrutura é modular e documentada, mas o arquivo Python inválido e endpoints sem autenticação prejudicam a avaliação.
- **Reprodutível:** bom. É a versão com melhor documentação de experimentos, recursos, limpeza e limitações.

## Discussão Comparativa

### Qualidade do Código

[coder-deepseek](/home/leas/LEAS/coder-deepseek) tem o código mais simples e direto. Isso favorece revisão, mas concentra responsabilidades em [api/app.py](/home/leas/LEAS/coder-deepseek/api/app.py:1). [coder-gemini](/home/leas/LEAS/coder-gemini) tem separação em módulos, mas mantém UI inline extensa em [routes_base.py](/home/leas/LEAS/coder-gemini/gateway/gwapi_app/routes_base.py:7) e usa permissões amplas no boot. [coder-gpt](/home/leas/LEAS/coder-gpt) tem a melhor arquitetura Flask, com factory e blueprints, mas é prejudicado pelo arquivo inválido [gateway/gwapi_app/init.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/init.py:7).

**Melhor neste critério:** [coder-gpt](/home/leas/LEAS/coder-gpt), com ressalva forte sobre o arquivo inválido.

**Melhor núcleo de firewall:** [coder-deepseek](/home/leas/LEAS/coder-deepseek).

### Organização do Projeto

[coder-gpt](/home/leas/LEAS/coder-gpt) é o mais organizado, com [gateway/gwapi_app](/home/leas/LEAS/coder-gpt/gateway/gwapi_app), templates, dados persistidos, artefatos gerados e documentos auxiliares. [coder-deepseek](/home/leas/LEAS/coder-deepseek) é organizado de forma simples e didática, com diretórios separados para Docker, Kea, nftables e API. [coder-gemini](/home/leas/LEAS/coder-gemini) tem boa separação básica, mas o bind mount de [gateway](/home/leas/LEAS/coder-gemini/gateway) como `/app` mistura código e estado.

**Melhor neste critério:** [coder-gpt](/home/leas/LEAS/coder-gpt).

### Clareza da Documentação

[coder-gpt](/home/leas/LEAS/coder-gpt) tem o README mais próximo de um pacote científico reprodutível, com experimentos claros e limitações. [coder-deepseek](/home/leas/LEAS/coder-deepseek) também é claro e objetivo, mas falta título formal do artigo e seção de selos. [coder-gemini](/home/leas/LEAS/coder-gemini) documenta uso básico, mas não atende várias seções esperadas pelo SBSeg.

**Melhor neste critério:** [coder-gpt](/home/leas/LEAS/coder-gpt).

### Facilidade de Instalação

Os três usam Docker Compose e imagens Ubuntu. [coder-deepseek](/home/leas/LEAS/coder-deepseek) é o mais simples para subir porque possui defaults no [docker-compose.yml](/home/leas/LEAS/coder-deepseek/docker-compose.yml:8). [coder-gemini](/home/leas/LEAS/coder-gemini) e [coder-gpt](/home/leas/LEAS/coder-gpt) dependem de `.env` local, mas isso está documentado nos READMEs atuais.

**Melhor neste critério:** empate prático entre [coder-deepseek](/home/leas/LEAS/coder-deepseek) e [coder-gpt](/home/leas/LEAS/coder-gpt).

### Facilidade de Execução

Todos os três executaram com `docker compose up --build -d`. [coder-deepseek](/home/leas/LEAS/coder-deepseek) expõe API em `8080`, [coder-gemini](/home/leas/LEAS/coder-gemini) em `5000`, e [coder-gpt](/home/leas/LEAS/coder-gpt) publica `8080` para a porta interna `5000`. A divergência de portas é aceitável isoladamente, mas dificulta comparação automatizada.

**Melhor neste critério:** empate funcional, com pequena vantagem para [coder-deepseek](/home/leas/LEAS/coder-deepseek) pela simplicidade.

### Cobertura de Testes

Nenhum repositório possui cobertura de testes automatizados efetiva. [coder-deepseek](/home/leas/LEAS/coder-deepseek) inclui [scripts/test_connectivity.sh](/home/leas/LEAS/coder-deepseek/scripts/test_connectivity.sh:1), mas o arquivo está vazio. [coder-gemini](/home/leas/LEAS/coder-gemini) e [coder-gpt](/home/leas/LEAS/coder-gpt) não apresentam testes automatizados ou CI observáveis.

**Melhor neste critério:** nenhum; todos precisam de atenção.

### Reprodutibilidade

[coder-gpt](/home/leas/LEAS/coder-gpt) é o mais forte em reprodutibilidade documental: descreve reivindicações, comandos, recursos esperados e limpeza. [coder-deepseek](/home/leas/LEAS/coder-deepseek) também é bom nesse aspecto, embora menos completo. [coder-gemini](/home/leas/LEAS/coder-gemini) tem validação básica, mas carece de experimentos por reivindicação e de limites/segurança.

**Melhor neste critério:** [coder-gpt](/home/leas/LEAS/coder-gpt).

### Sustentabilidade do Projeto

[coder-gpt](/home/leas/LEAS/coder-gpt) tem melhor organização e documentação, mas precisa corrigir o arquivo Python inválido e a segurança dos endpoints. [coder-deepseek](/home/leas/LEAS/coder-deepseek) é sustentável pela simplicidade, mas precisa modularizar se crescer. [coder-gemini](/home/leas/LEAS/coder-gemini) precisa reduzir acoplamento de UI, melhorar permissões e estruturar documentação científica.

**Melhor neste critério:** [coder-gpt](/home/leas/LEAS/coder-gpt), após correção imediata de higiene de código.

## Matriz Sintética

Escala: 1 = fraco, 2 = parcial, 3 = adequado, 4 = bom, 5 = excelente.

| Critério | [coder-deepseek](/home/leas/LEAS/coder-deepseek) | [coder-gemini](/home/leas/LEAS/coder-gemini) | [coder-gpt](/home/leas/LEAS/coder-gpt) |
| --- | ---: | ---: | ---: |
| Qualidade do código | 3 | 3 | 3 |
| Organização do projeto | 4 | 3 | 4 |
| Clareza da documentação | 4 | 2 | 5 |
| Facilidade de instalação | 4 | 4 | 4 |
| Facilidade de execução | 4 | 4 | 4 |
| Cobertura de testes | 1 | 1 | 1 |
| Reprodutibilidade | 4 | 2 | 5 |
| Sustentabilidade | 3 | 2 | 4 |
| Aderência ao problema de cibersegurança | 4 | 3 | 2 |

## Ranking Geral

1. [coder-gpt](/home/leas/LEAS/coder-gpt): melhor artefato científico geral, principalmente por documentação, organização, persistência e experimentos. Precisa de correções imediatas em segurança e higiene de código.
2. [coder-deepseek](/home/leas/LEAS/coder-deepseek): melhor implementação do firewall base e excelente simplicidade operacional. Fica atrás por menor modularidade, ausência de testes e lacunas formais no README.
3. [coder-gemini](/home/leas/LEAS/coder-gemini): funcional e demonstrável via dashboard, mas menos aderente ao padrão SBSeg e com maior acoplamento técnico/documental.

## Reflexão Crítica

A melhor solução como artefato científico é [coder-gpt](/home/leas/LEAS/coder-gpt), porque oferece a experiência mais completa para um avaliador: README amplo, documentos auxiliares, instalação clara, teste mínimo, experimentos por reivindicação, endpoints listados e estratégia explícita de limpeza. Esse conjunto favorece os critérios SBSeg de funcionalidade, sustentabilidade e reprodutibilidade. Ainda assim, ele não deve ser considerado pronto sem correções: [gateway/gwapi_app/init.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/init.py:7) falha em validação estática, o status DHCP via Control Agent não retornou sucesso, os endpoints REST de firewall aceitam escrita sem autenticação, e o firewall gerado por [gateway/start-gateway.sh](/home/leas/LEAS/coder-gpt/gateway/start-gateway.sh:96) é permissivo demais para a proposta de cibersegurança.

As diferenças observadas são relevantes. [coder-deepseek](/home/leas/LEAS/coder-deepseek) parece ter sido construído com foco em um núcleo técnico pequeno e verificável; sua política nftables é a mais convincente para demonstrar isolamento e controle de tráfego. [coder-gemini](/home/leas/LEAS/coder-gemini) parece priorizar interação visual e manipulação de reservas DHCP; por isso é atraente para demonstração, mas menos rigoroso como submissão científica. [coder-gpt](/home/leas/LEAS/coder-gpt) prioriza empacotamento acadêmico e organização, mas deixou problemas técnicos que contradizem parte da narrativa de segurança.

O que os três têm de bom é a escolha de uma arquitetura adequada para avaliação de artefatos: Docker Compose, Kea DHCP, nftables, clientes reais em LAN isolada e comandos verificáveis. Os três conseguiram executar um cenário mínimo de DHCP e NAT no ambiente avaliado. Todos usam imagens Ubuntu e evitam exigir instalação manual de Kea ou nftables no host, o que favorece avaliadores.

O que os três têm de ruim é a ausência quase total de testes automatizados. Para uma submissão SBSeg, depender apenas de comandos manuais no README reduz confiança e aumenta custo de revisão. Além disso, todos usam credenciais padrão, HTTP sem TLS e capacidades elevadas de container. Esses pontos são aceitáveis em laboratório, mas precisam de documentação de ameaça mais clara e, quando possível, mitigação.

Os pontos que merecem atenção imediata são:

- criar testes automatizados de smoke test para todos os repositórios;
- adicionar seção explícita de selos considerados nos READMEs;
- corrigir [gateway/gwapi_app/init.py](/home/leas/LEAS/coder-gpt/gateway/gwapi_app/init.py:7);
- alinhar o Control Agent no [coder-gpt](/home/leas/LEAS/coder-gpt) para que `/api/dhcp/status` retorne sucesso;
- proteger endpoints de escrita no [coder-gpt](/home/leas/LEAS/coder-gpt);
- fortalecer o firewall base no [coder-gpt](/home/leas/LEAS/coder-gpt) e no [coder-gemini](/home/leas/LEAS/coder-gemini) com política stateful mais restritiva;
- transformar [scripts/test_connectivity.sh](/home/leas/LEAS/coder-deepseek/scripts/test_connectivity.sh:1) em um teste real;
- separar templates/UI/código no [coder-gemini](/home/leas/LEAS/coder-gemini) para melhorar manutenção;
- evitar permissões amplas como `chmod -R 777` em scripts de inicialização;
- publicar uma release estável ou pacote arquivado para melhorar o selo de Artefatos Disponíveis.

## Parecer Final

Os três artefatos são funcionais em nível de laboratório e endereçam o mesmo problema de segurança de redes containerizadas. Para submissão científica, [coder-gpt](/home/leas/LEAS/coder-gpt) deve ser escolhido como base principal, desde que corrija imediatamente os problemas de segurança e validação estática. Se o critério prioritário for a coerência da política de firewall e a demonstração de isolamento stateful, [coder-deepseek](/home/leas/LEAS/coder-deepseek) é a referência técnica mais forte. [coder-gemini](/home/leas/LEAS/coder-gemini) pode contribuir com ideias de interface e reservas DHCP, mas exige maior reestruturação documental para atender plenamente aos critérios do SBSeg 2026.
