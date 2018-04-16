# BOT
import java.util.List;
import com.pengrad.telegrambot.TelegramBot;
import com.pengrad.telegrambot.TelegramBotAdapter;
import com.pengrad.telegrambot.model.File;
import com.pengrad.telegrambot.model.Update;
import com.pengrad.telegrambot.model.request.ChatAction;
import com.pengrad.telegrambot.model.request.InlineKeyboardButton;
import com.pengrad.telegrambot.model.request.InlineKeyboardMarkup;
import com.pengrad.telegrambot.model.request.Keyboard;
import com.pengrad.telegrambot.model.request.KeyboardButton;
import com.pengrad.telegrambot.model.request.ReplyKeyboardMarkup;
import com.pengrad.telegrambot.model.request.ReplyKeyboardRemove;
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
		// objeto responsavel por gerenciar o envio de a��es do chat
		BaseResponse baseResponse;

		// controle de off-set. A partir deste ID serao lidas as mensagens
		// pendentes na fila
		int m = 0;

		// loop infinito pode ser alterado por algum timer de intervalo curto
		while (true) {

			// executa comando no Telegram para obter as mensagens pendentes a partir de um
			// off-set (limite inicial)
			updatesResponse = bot.execute(new GetUpdates().limit(100).offset(m));

			// lista de mensagens
			List<Update> updates = updatesResponse.updates();

			// an�lise de cada acao da mensagem
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
						//sendResponse = bot.execute(new SendMessage(update.message().chat().id(),
						//		"Número enviado com sucesso\n"));

						// Criação de Keyboard
							sendResponse = bot.execute(new SendMessage(update.message().chat()
									.id(), "Número enviado com sucesso\n").replyMarkup(new ReplyKeyboardMarkup(
											new String[] { "Abrir Menu" })));

					} else if (update.message().location() != null) {
						//sendResponse = bot.execute(new SendMessage(update.message().chat().id(),
						//		"Localização enviada com sucesso\n"));

							
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
													new KeyboardButton[] { new KeyboardButton("Fornecer localização")
															.requestLocation(true) })));

							//Quantidade_de_Galoes
						} else if (update.message().text().equals("Quantidade de Galões")) {
							
							System.out.println("Quantidade de galões");
							String qntd_gal = (update.message().text());


							
							// keyboard inline - callBackData
						} else if (update.message().text().equals("Confirmar Pedido")) {
							sendResponse = bot
									.execute(new SendMessage(update.message().chat().id(), "Confirmação de pedido")
											.replyMarkup(new InlineKeyboardMarkup(new InlineKeyboardButton[] {
													new InlineKeyboardButton("Confirmar pedido").callbackData("Pedido confirmado com sucesso")})));

							sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"Obrigado pela preferencia\nVolte sempre :)"));

							
							

						} else {
							//sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"Bem Vindo ao Delivery de água\n\n"));

							//sendResponse = bot.execute(new SendMessage(update.message().chat().id(),"Informe seu contato e sua localização"));

							// Criação de Keyboard
							sendResponse = bot.execute(new SendMessage(update.message().chat()
									.id(), "Bem Vindo ao Delivery de água\n\n").replyMarkup(new ReplyKeyboardMarkup(
											new String[] { "Abrir Menu" })));

						}


						

					}

				}

			}

		}

	}

}
