---
# Mobile SDK Android (Beta)

Com o MoipSDK você pode receber pagamentos no seu aplicativo sem se preocupar com criptografia e de uma maneira fácil e simples.

Antes iniciar a integração de seu aplicativo com o SDK Mobile Android, entre no site do Moip. Lá você encontrará os primeiros passos para criar sua conta Moip.
https://moip.com.br/moip-apps/

---

##Release Notes

###### Versão 1.0 Beta 4 - 26/08/2014
* Adicionado a versão JAR da SDK
* Adicionado dependencias da SDK

###### Versão 1.0 Beta 3 - 24/08/2014
* Ajustes na criptografia

###### Versão 1.0 Beta 2 - 22/08/2014
* Ajustes para criação do pagamento com cartão criptografado

###### Versão 1.0 Beta 1 - 21/08/2014
* Melhorias nos componentes de cartão de crédito e cvc do Moip

#Como usar a SDK Android

Estamos trabalhando para disponibilizar o quanto antes o **SDK Android sample**, mas você já pode se familiarizar com sua estrutura.

Veja abaixo o tutorial passo-a-passo como integrar com a SDK para Android.

###1. Dependências da SDK

Para utilizar a SDK do Moip, antes é preciso adicionar as dependencias dela. Há um zip com as depêndencias neste repositório.
Caso utilize o gradle adicione apenas as seguintes linhas.

```
    compile 'com.madgag.spongycastle:pkix:1.51.0.0'
    compile 'com.netflix.feign:feign-gson:6.1.2'
    compile 'commons-codec:commons-codec:1.9'
```


###2. Iniciando a SDK

O primeiro passo é iniciar a SDK passando seu Token, Key, Chave Publica RSA e o ambiente que o pagamento será criado.

```java
	Moip moip = new Moip(Environment.SANDBOX, TOKEN, KEY, PUBLIC_KEY);
```

###3. Utilizando os componentes do Moip

Para adicionar os componentes de cartão de crédito e cvv do Moip a seu aplicativo, crie os seguintes campos em sua interface.
```xml
	<com.moip.sdk.lib.components.MoipCreditCardEditText
            android:id="@+id/moip_credit_card_edit_text"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:hint="Credit Card"/>
            
        <com.moip.sdk.lib.components.MoipCVCEditText
            android:id="@+id/moip_cvc_edit_text"
            android:layout_width="96dp"
            android:layout_height="wrap_content"
            android:hint="CVV" />
```

Declaração dos componentes

```java

	private MoipCreditCardEditText moipCC;
	private MoipCVCEditText moipCVC;
```

Inicializando os componentes

```java

        moipCVC = (MoipCVCEditText) findViewById(R.id.moip_cvc_edit_text);
        moipCC = (MoipCreditCardEditText) findViewById(R.id.moip_credit_card_edit_text);
```


###4. Capturando os dados do pagamento

```java
	Payment payment = new Payment();
	payment.setMoipOrderID(moipOrderID);
	payment.setInstallmentCount(1);

	CreditCard creditCard = new CreditCard();
	creditCard.setExpirationMonth(1);
	creditCard.setExpirationYear(17);
	creditCard.setNumber(moipCC.getText().toString());
	creditCard.setCvc(moipCVC.getText().toString());
        creditCard.setBrand(moipCC.whatBrand());

	Holder holder = new Holder();
	holder.setBirthdate("1990-7-24");
	holder.setFullname("Hip Bot");

	TaxDocument taxDocument = new TaxDocument();
	taxDocument.setNumber("40328944011");
	taxDocument.setType(DocumentType.CPF.getDescription());

	Phone phone = new Phone();
	phone.setCountryCode(55);
	phone.setAreaCode(11);
	phone.setNumber("955555555");

	holder.setTaxDocument(taxDocument);
	holder.setPhone(phone);
	creditCard.setHolder(holder);

	FundingInstrument fundingInstrument = new FundingInstrument();
	fundingInstrument.setCreditCard(creditCard);
	fundingInstrument.setMethod("CREDIT_CARD");

	payment.setFundingInstrument(fundingInstrument);
```

###5. Criando o pagamento (PAYMENT)

Após o preenchimento do formulário de pagamento, você já pode enviar os dados para o Moip efetuar a transação.

```java   

	moip.createPayment(payment, new MoipCallback<Payment>() {
        	@Override
                public void success(Payment payment) {
			//Lógica caso o pagamento tenha sido realizado com sucesso
                }

                @Override
                public void failure(List<MoipError> moipErrorList) {
			//Lógica caso haja algum erro ao criar o pagamento
                }
            });
```
