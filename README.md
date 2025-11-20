# SmartParking

## Casos de uso

### Caso de Uso: Entrada no Estacionamento

**Controladora responsável:** *Controladora da Cancela de Entrada*

1. O usuário para o carro diante da cancela de entrada.
2. O usuário pressiona o botão da cancela para iniciar o processo de entrada.
3. A câmera lê a placa do veículo e envia a informação para a controladora.
4. A controladora verifica se a placa está na lista de veículos autorizados:

   * **Placa autorizada:** a controladora define o `paymentTimestamp` para uma data muito no futuro.
   * **Placa não autorizada:** o `paymentTimestamp` não é definido.
5. A controladora envia ao **CardWriter** os dados do cartão: `entryTimestamp`, `licensePlate` e, quando aplicável, `paymentTimestamp`.
6. A controladora envia ao **CardEmitter** o comando para ejetar o cartão.
7. O usuário retira o cartão ejetado.
8. A controladora envia à cancela o comando para abrir.
9. A controladora aguarda o sinal do sensor de presença instalado após a cancela.
10. O usuário começa a atravessar a cancela com o veículo.
11. Quando o sensor detecta a presença do veículo, a controladora aguarda até que o sinal cesse (indicando que o veículo terminou de passar).
12. Após a passagem completa do veículo, a controladora envia o comando para fechar a cancela.
13. A controladora reinicia o processo, voltando ao estado de espera pelo próximo veículo.


<br>

### Caso de Uso: Saída do Estacionamento

**Controladora responsável:** *Controladora da Cancela de Saída*

1. O usuário para o veículo diante da cancela de saída.
2. O usuário insere o cartão no leitor da cancela, de forma que ele não possa ser removido sem autorização da controladora.
3. O **CardReader** lê os dados do cartão (`entryTimestamp`, `licensePlate` e, quando existir, `paymentTimestamp`) e envia essas informações para a controladora.
4. A câmera captura a imagem da placa do veículo e envia a leitura para a controladora.
5. A controladora verifica se a placa lida pela câmera corresponde à placa registrada no cartão:

   * **Placa correspondente:** o processo continua.
   * **Placa divergente:** a controladora sinaliza erro e não autoriza a abertura da cancela.
6. A controladora consulta o `paymentTimestamp` para verificar se o ticket está quitado e dentro do prazo máximo permitido para saída:

   * **Pagamento válido:** o usuário está autorizado a prosseguir.
   * **Pagamento pendente ou expirado:** a controladora sinaliza erro e não autoriza a abertura da cancela.
7. Se todas as validações forem aprovadas, a controladora envia ao **CardHandler** o comando para coletar o cartão inserido.
8. A controladora envia à cancela o comando para abrir.
9. A controladora aguarda o sinal do sensor de presença localizado após a cancela.
10. O usuário inicia a travessia com o veículo.
11. Quando o sensor detecta a passagem do veículo, a controladora aguarda até que o sinal cesse (indicando que o carro concluiu a travessia).
12. Após a passagem completa do veículo, a controladora envia o comando para fechar a cancela.
13. A controladora retorna ao estado de espera pelo próximo veículo que irá sair.

<br>

#### Fluxo de Exceção: Pagamento Pendente, Cartão Inválido ou Placa Não Correspondente

1. Caso a saída não seja autorizada, a controladora aciona um alerta para o usuário (como luz vermelha ou mensagem na tela).
2. A controladora envia ao **CardHandler** o comando para ejetar o cartão.
3. O usuário retira o cartão ejetado.
4. O usuário retorna com o veículo para fora da área da cancela.
5. A controladora reinicia o processo e volta ao estado de espera pelo próximo veículo.

<br>

