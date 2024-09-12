# Captura de tela do circuito
![Captura de tela 2024-09-12 184948](https://github.com/user-attachments/assets/de25a00a-6b0a-481c-aeca-88a75cb576a0)

# alarmClock_arduino
- Este projeto consiste em um relógio com alarme que permite a edição do horário atual e do alarme.
- Todo o processamento do programa é feito somente com o arduino
- Nenhum módulo RTC (Real Time Clock) foi utilizado no projeto
- O alarme também é capaz de disparar um sinal sonoro quando o horário do relógio é o mesmo do horário do alarme
- O alarme quando disparado, pode ser desligado quando o usuário aproxima a sua mão do relógio, a partir de um sensor ultrasônico
- Mesmo com o Arduino não sendo capaz de calcular grandes espaços de tempo com precisão, o código conta com um sistema de ajuste, que amortiza consideravelmente esse efeito

## Motivações do projeto
- Todos os projetos que eu pesquisei sobre um alarme com arduino utiliza o módulo RTC (Real Time Clock), que é uma placa que já processa todo o sistema de um relógio convencional
- Fazer um projeto desafiante do zero
- Implementar o conceito de multi thread em um microcontrolador que não permite mais de um thread
- Mesmo o arduino não ser capaz de processar mais de uma operação, o código permite que seja verificado se um botão foi clicado, ou foi pressionado por mais de 2 segundos
- O código também permite que o usuário mude de páginas no display, edite o horário do alarme, e desligar o alarme sem atrasar ou adiantar o relógio


## Como funciona:
- Na tela do relógio, caso o botão verde for clicado, o alarme é ligado ou desligado. O mesmo ocorre na tela do alarme
- Na tela do relógio, caso o botão vermelho for clicado, muda para a tela do alarme
- Na tela do alarme, se o botão vermelho for clicado, volta para a tela do relógio
- Na tela do relógio, se o botão vermelho for clicado por mais de 2 segundo o sistema entra em modo de edição:
    - clicando no botão vermelho a hora aumenta, no azul a hora diminui
    - Em seguida, clique no botão vermelho, agora os minutos podem ser editados com os mesmos botões
    - Depois, clique no botão vermelho novamente para sair do modo de edição. Agora o relógio volta a funcionar

- Na tela do alarme, se o botão vermelho for clicado por mais de 2 segundo o sistema entra em modo de edição:
    - clicando no botão vermelho a hora aumenta, no azul a hora diminui
    - Em seguida, clique no botão vermelho, agora os minutos podem ser editados com os mesmos botões
    - Depois, clique no botão vermelho novamente para sair do modo de edição

