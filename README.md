# Desafio: Phishing com SEToolkit (DIO)

**Autor:** Luxlox
**Repositório:** <https://github.com/luxlox/cibersecurity-desafio-phishing/>  
**Data:** <25/10/2025>

## Objetivo
Recriar/aperfeiçoar o projeto do instrutor para entender o funcionamento do Social-Engineer Toolkit (SET) no contexto de captura de credenciais em ambiente de teste controlado.

## Descrição do Projeto
Resumo do que foi feito:
- Usei o SEToolkit para clonar um template de página (em ambiente isolado).
- Realizei testes com o **Web Template** do SET para capturar POSTs.
- Registrei evidências (prints) no repositório em `/images`.

## Passos executados (resumido)
1. Iniciei o SET → `Social-Engineering Attacks` → `Website Attack Vectors` → `Credential Harvester`.  
2. Usei a opção **Web Template** / clone local para capturar POSTs.  
3. Testei com página local e confirmei captura via output do harvester.  
4. Salvei evidência: `/images/harvester_result.png`.

## Preparação da rede e configuração da VM (detalhado)
Para garantir um ambiente controlado e reproduzível, configurei a VM e a rede do Kali da seguinte forma:

### Máquina virtual
- VirtualBox (ou outro, conforme seu ambiente).  
- Snapshot criado antes dos testes para fácil rollback.  
- Recursos: 2 vCPU, 4 GB RAM (ajustável), 1 interface de rede usada em modo *Host-only* / *NAT + Host-only* (dependendo do laboratório).  
- Sistema: Kali Linux (VM), atualizado (`sudo apt update && sudo apt upgrade -y`).

### Rede e endereçamento
- Host-only interface atribuída com IP estático dentro da sub-rede host-only  

### Apache / Listener
- Apache foi usado como servidor que serve o conteúdo clonado do SET (`/var/www/html/`).  
- Quando o SET detectou Apache ativo ele solicitou parar o serviço para bindar a porta 80 — aceitei para permitir que o harvester rodasse.  
- Logs verificados: `/var/log/apache2/access.log` e `/var/log/apache2/error.log`.


## Gotchas / Confusões e decisões técnicas
- **Por que não capturei diretamente Facebook/Instagram?**  
Sites reais modernos usam HTTPS/HSTS, JS/AJAX, tokens dinâmicos e endpoints distintos — o SET copia a aparência, mas o comportamento do JavaScript frequentemente envia dados por XHR, então o harvester não vê os pares `campo=valor`.  
- **Como contornei isso para fins do desafio?**  
Usei o **Web Template** (ou um HTML simples) que envia `application/x-www-form-urlencoded` com `name=email` e `name=pass`, garantindo que o SET parseie e grave nos relatórios.  
- **Permissões e relatórios:**  
Os relatórios do SET ficam em `/root/.set/reports`. Se não houver valores visíveis, verifique permissões, se o harvester recebeu o POST (logs do Apache / tcpdump / socat) e se o navegador enviou para o IP correto.

## Como reproduzir (mínimo)
1. Preparar VM Kali e rede conforme seção anterior.  
2. Rodar `setoolkit` → `Website Attack Vectors` → `Credential Harvester` → `Web Templates` (ou clone apontando para `http://<seu_ip>/testform.html`).  
3. Abrir o site no navegador com perfil limpo e submeter usuário/senha fictícios.  
4. Conferir saída do harvester no terminal do SET e os relatórios em `/root/.set/reports`.  
5. Salvar screenshot em `/images/harvester_result.png`.

## Evidências
- `/images/harvester_result.png` — print do terminal do SET mostrando o "WE GOT A HIT" e os parâmetros capturados.

## Observações finais e aprendizado
- Ambiente controlado é essencial: evite testar em redes abertas ou contra domínios reais sem autorização.  
- Em laboratório, é educativo comparar: (a) clonar site real e observar limitações; (b) usar template simples para validar o fluxo de captura.  


## Referências
- Repositório-base do instrutor: https://github.com/cassiano-dio/cibersecurity-desafio-phishing  
- SEToolkit: https://github.com/trustedsec/social-engineer-toolkit
