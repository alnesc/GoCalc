package main

import (
	"fmt"
	"strconv"

	tgbotapi "github.com/go-telegram-bot-api/telegram-bot-api/v5"
)

const TOKEN = ""

var bot *tgbotapi.BotAPI
var chatID int64

type UserState struct {
	Step int
	Rk1  float64
	Rk2  float64
}

var userStates = make(map[int64]*UserState)

func connectWithTelegram() {
	var err error
	if bot, err = tgbotapi.NewBotAPI(TOKEN); err != nil {
		panic("Cannot connect to Telegram")
	}
}

func sendMessage(chatID int64, msg string) {
	msgConfig := tgbotapi.NewMessage(chatID, msg)
	bot.Send(msgConfig)
}

func isValidScore(input string) (float64, bool) {
	score, err := strconv.ParseFloat(input, 64)
	if err != nil || score < 0 || score > 100 {
		return 0, false
	}
	return score, true
}

func calculateNeededScores(rk1, rk2 float64) (float64, float64, bool) {
	averageScore := (rk1 + rk2) / 2
	if averageScore < 50 {
		return 0, 0, false
	}
	needToAvoidFail := (50 - 0.3*(rk1+rk2)) / 0.4
	if needToAvoidFail < 50 {
		needToAvoidFail = 50
	}
	needToKeepScholarship := (70 - 0.3*(rk1+rk2)) / 0.4
	if needToKeepScholarship < 50 {
		needToKeepScholarship = 50
	}
	return needToAvoidFail, needToKeepScholarship, true
}

func handleUserInput(update tgbotapi.Update, state *UserState) {
	switch state.Step {
	case 0:
		if score, valid := isValidScore(update.Message.Text); valid {
			state.Rk1 = score
			state.Step = 1
			sendMessage(update.Message.Chat.ID, "Введите балл за РК2:")
		} else {
			sendMessage(update.Message.Chat.ID, "Напишите правильно, РК1 (0-100):")
		}
	case 1:
		if score, valid := isValidScore(update.Message.Text); valid {
			state.Rk2 = score
			needToAvoidFail, needToKeepScholarship, valid := calculateNeededScores(state.Rk1, state.Rk2)
			if valid {
				response := fmt.Sprintf("Чтобы не остаться на летний семестр : %.2f баллов нужно набрать\n", needToAvoidFail)
				if needToKeepScholarship > 100 {
					response += "К сожелению вы упали со стипендии"
				} else {
					response += fmt.Sprintf("Чтобы сохранить стипендию: %.2f баллов нужно набрать", needToKeepScholarship)
				}
				sendMessage(update.Message.Chat.ID, response)
			} else {
				sendMessage(update.Message.Chat.ID, "К сожелению,Вы остаетесь на летний семестр")
			}
			delete(userStates, update.Message.Chat.ID)
		} else {
			sendMessage(update.Message.Chat.ID, "Напишите правильно, РК2 (0-100):")
		}
	}
}

func main() {
	connectWithTelegram()

	updateConfig := tgbotapi.NewUpdate(0)
	updateConfig.Timeout = 60

	updates := bot.GetUpdatesChan(updateConfig)

	for update := range updates {
		if update.Message != nil {
			chatID = update.Message.Chat.ID

			if update.Message.Text == "/start" {
				sendMessage(chatID, "Напиши свои баллы по РК1 и РК2 а я скажу сколько Вам нужно набрать на сессии,чтобы не остаться на летний семестр или сохранить стипендию ")
				userStates[chatID] = &UserState{Step: 0}
				sendMessage(chatID, "Введите балл за РК1:")
			} else if state, exists := userStates[chatID]; exists {
				handleUserInput(update, state)
			}
		}
	}
}
