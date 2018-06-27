# BOT
import java.util.ArrayList;
import java.util.List;
import com.pengrad.telegrambot.TelegramBot;
import com.pengrad.telegrambot.TelegramBotAdapter;
import com.pengrad.telegrambot.model.CallbackQuery;
import com.pengrad.telegrambot.model.File;
import com.pengrad.telegrambot.model.Update;
import com.pengrad.telegrambot.model.request.ChatAction;
import com.pengrad.telegrambot.model.request.InlineKeyboardButton;
import com.pengrad.telegrambot.model.request.InlineKeyboardMarkup;
import com.pengrad.telegrambot.model.request.InlineQueryResult;
import com.pengrad.telegrambot.model.request.InlineQueryResultPhoto;
import com.pengrad.telegrambot.model.request.Keyboard;
import com.pengrad.telegrambot.model.request.KeyboardButton;
import com.pengrad.telegrambot.model.request.ReplyKeyboardMarkup;
import com.pengrad.telegrambot.model.request.ReplyKeyboardRemove;
import com.pengrad.telegrambot.request.AnswerInlineQuery;
import com.pengrad.telegrambot.request.EditMessageText;
import com.pengrad.telegrambot.request.GetFile;
import com.pengrad.telegrambot.request.GetUpdates;
import com.pengrad.telegrambot.request.SendChatAction;
import com.pengrad.telegrambot.request.SendMessage;
import com.pengrad.telegrambot.response.BaseResponse;
import com.pengrad.telegrambot.response.GetFileResponse;
import com.pengrad.telegrambot.response.GetUpdatesResponse;
import com.pengrad.telegrambot.response.SendResponse;
import java.util.Scanner;

public class Bot {

	public static void main(String[] args) throws InterruptedException {

		// Criacao do objeto bot com as informacoes de acesso
		TelegramBot bot = TelegramBotAdapter.build("562353293:AAEZSKi1HoYiCRWKRn9_AyOp0vRjdKsOCHo");

		// objeto responsavel por receber as mensagens
		GetUpdatesResponse updatesResponse;
		// objeto responsavel por gerenciar o envio de respostas
		SendResponse sendResponse;
		// objeto responsavel por gerenciar o envio de a??es do chat
		BaseResponse baseResponse;

		// controle de off-set. A partir deste ID serao lidas as mensagens
		// pendentes na fila
		int m = 0;
		int qnt = 0;
		Float val_gal = (float) 7.90;
		Float preco = null;
		Float l3 = null;
		Float l4 = null;
		String phone = "null";
		String url = null;
		String qnt_string = null;
		String f_pag = null;
		String firstName = null;
		String lastName = null;
		String username = null;

		// loop infinito pode ser alterado por algum timer de intervalo curto
		while (true) {
			updatesResponse = bot.execute(new GetUpdates().limit(100).offset(m));

			// lista de mensagens
			List<Update> updates = updatesResponse.updates();

			// an?lise de cada acao da mensagem
			for (Update update : updates) {

				// atualizacao do off-set
				m = update.updateId() + 1;

				if (update.callbackQuery() != null) {

					sendResponse = bot.execute(new SendMessage(update.callbackQuery().message().chat().id(),
							update.callbackQuery().data()));

				} else {
					System.out.println("Recebendo mensagem:" + update.message().text());

					// envio de "Escrevendo" antes de enviar a resposta
					baseResponse = bot
							.execute(new SendChatAction(update.message().chat().id(), ChatAction.typing.name()));
					// verificacao de acao de chat foi enviada com sucesso
					System.out.println("Resposta de Chat Action Enviada?" + baseResponse.isOk());

					// enviando numero de contato recebido

					if (update.message().contact() != null) {
						phone= update.message().contact().phoneNumber();

						firstName = update.message().chat().firstName();
						lastName = update.message().chat().lastName();
						username = update.message().chat().username();

						sendResponse = bot.execute(new SendMessage(update.message().chat()
								.id(),"Número " + update.message().contact().phoneNumber() + " enviado").replyMarkup(new ReplyKeyboardMarkup(
										new String[] { "Abrir Menu" })));

					} else if (update.message().location() != null) {
						sendResponse = bot.execute(new SendMessage(update.message().chat().id(),
								"Endereço enviado"));
					}

					if (update.message().text() != null) {

						// Criação de Keyboard
						sendResponse = bot.execute(new SendMessage(update.message().chat()
								.id(), " ").replyMarkup(new ReplyKeyboardMarkup(
										new String[] { "Abrir Menu" })));

					} else if (update.message().location() != null) {
						//sendResponse = bot.execute(new SendMessage(update.message().chat().id(),
						//		"Localização enviada com sucesso\n"));

						l3= update.message().location().latitude();
						l4= update.message().location().longitude();
						url = "http://maps.google.com/maps?q=" + l3 + "," + l4;

						// Criação de Keyboard
						sendResponse = bot.execute(new SendMessage(update.message().chat()
								.id(), "Localização enviada com sucesso\n").replyMarkup(new ReplyKeyboardMarkup(
										new String[] { "Abrir Menu" })));
					}

					if (update.message().text() != null) {


						// Criação de Keyboard
						if (update.message().text().equals("Abrir Menu")) {
							sendResponse = bot.execute(new SendMessage(update.message().chat()
									.id(), "Menu").replyMarkup(new ReplyKeyboardMarkup(
											new String[] { "Fornecer Contato" },
											new String[] { "Fornecer Localização" },
											new String[] { "Quantidade de Galões" },
											new String[] { "Forma de Pagamento" },
											new String[] { "Confirmar Pedido" })));

							// Pedir contato
						} else if (update.message().text().equals("Fornecer Contato")) {
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(), "Pedindo contato")
									.replyMarkup(new ReplyKeyboardMarkup(new KeyboardButton[] {
											new KeyboardButton("Fornecer contato").requestContact(true) })));


							// Pedir localização
						} else if (update.message().text().equals("Fornecer Localização")) {
							sendResponse = bot
									.execute(new SendMessage(update.message().chat().id(), "Pedindo Localização")
											.replyMarkup(new ReplyKeyboardMarkup(
													new KeyboardButton[] { new KeyboardButton("Fornecer Localização")
															.requestLocation(true) })));

							//Quantidade_de_Galoes
						} else if (update.message().text().equals("Quantidade de Galões")) {
							sendResponse = bot.execute(new SendMessage(update.message().chat()
									.id(), "Menu").replyMarkup(new ReplyKeyboardMarkup(
											new String[] { "1", "2", "3",  },
											new String[] { "4", "5", "6",  },
											new String[] { "7", "8", "9",  }
											)));

							//Quantidade_de_Galoes
						} else if (update.message().text().equals("Forma de Pagamento")) {
							sendResponse = bot.execute(new SendMessage(update.message().chat()
									.id(), "Menu").replyMarkup(new ReplyKeyboardMarkup(
											new String[] { "Cartão de crédito",  },
											new String[] { "Cartão de débito",  },
											new String[] { "Dinheiro",  }
											)));


						} else if (update.message().text().equals("Cartão de crédito")) {
							f_pag="Cartão de crédito";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"Forma de pagamento confirmada").replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));

						} else if (update.message().text().equals("Cartão de débito")) {
							f_pag="Cartão de débito";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"Forma de pagamento confirmada").replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));

						} else if (update.message().text().equals("Dinheiro")) {
							f_pag="Dinheiro";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"Forma de pagamento confirmada").replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));

						} else if (update.message().text().equals("1")) {
							qnt= 1;
							preco=qnt*val_gal;
							qnt_string="1 Galão";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"1 Galão\n\nValor: R$"+preco).replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));


						} else if (update.message().text().equals("2")) {
							qnt= 2;
							preco=qnt*val_gal;
							qnt_string="2 Galões";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"2 Galões\n\nValor: R$"+preco).replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));


						} else if (update.message().text().equals("3")) {
							qnt= 3;
							preco=qnt*val_gal;
							qnt_string="3 Galões";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"3 Galões\n\nValor: R$"+preco).replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));


						} else if (update.message().text().equals("4")) {
							qnt= 4;
							preco=qnt*val_gal;
							qnt_string="4 Galões";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"4 Galões\n\nValor: R$"+preco).replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));


						} else if (update.message().text().equals("5")) {
							qnt= 5;
							preco=qnt*val_gal;
							qnt_string="5 Galões";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"5 Galões\n\nValor: R$"+preco).replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));


						} else if (update.message().text().equals("6")) {
							qnt= 6;
							preco=qnt*val_gal;
							qnt_string="6 Galões";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"6 Galões\n\nValor: R$"+preco).replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));


						} else if (update.message().text().equals("7")) {
							qnt= 7;
							preco=qnt*val_gal;
							qnt_string="7 Galões";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"7 Galões\n\nValor: R$"+preco).replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));


						} else if (update.message().text().equals("8")) {
							qnt= 8;
							preco=qnt*val_gal;
							qnt_string="8 Galões";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"8 Galões\n\nValor: R$"+preco).replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));


						} else if (update.message().text().equals("9")) {
							qnt= 9;
							preco=qnt*val_gal;
							qnt_string="9 Galões";
							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"9 Galões\n\nValor: R$"+preco).replyMarkup(new ReplyKeyboardMarkup(new String[] { "Abrir Menu" })));




							// keyboard inline - callBackData
						} else if (update.message().text().equals("Confirmar Pedido")) {
							if (preco == null || phone == "null" || url == null || 
									qnt_string == null || f_pag == null || firstName == null || lastName == null || username == null) {

								sendResponse = bot.execute(new SendMessage(update.message().chat()
										.id(), "Por favor, preencha todos os campos\n\n").replyMarkup(new ReplyKeyboardMarkup(
												new String[] { "Abrir Menu" })));

							}else {
								sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"Obrigado pela preferencia\nVolte sempre"));

								sendResponse = bot.execute(new SendMessage(550977389,
										"Nome: " + firstName + lastName + "\n"
										+"User Name: " + username + "\n"
										+"Quantidade: " + qnt_string + "\n"
										+"Valor: R$" + preco + "\n"
										+"Forma de Pagamento: " + f_pag + "\n"
										+"Número: " + phone + "\n"
										+"Localização: " + url + "\n"
								));
							}


						} else {
							// Criação de Keyboard
							sendResponse = bot.execute(new SendMessage(update.message().chat()
									.id(), "Bem Vindo ao Delivery de Água\n\n").replyMarkup(new ReplyKeyboardMarkup(
											new String[] { "Abrir Menu" })));



						}




					}

				}

			}

		}

	}

}
