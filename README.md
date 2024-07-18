Telegram Bot Project

This project demonstrates a simple Telegram bot that helps users calculate the required scores to avoid failing a semester or to keep their scholarship.

Requirements

    Go 1.22.3 or later

Setup

To get started,
Make sure you have Go installed. You can download it from the official Go website.

Initialize the Go module:

go mod init yourmodule

Add the necessary dependencies:

go mod tidy

Dependencies

This project uses the following dependency:

	•	github.com/go-telegram-bot-api/telegram-bot-api/v5 (v5.5.1)       //go.mod

You can add the specific version of the dependency to your go.mod file as follows:

require github.com/go-telegram-bot-api/telegram-bot-api/v5 v5.5.1 // indirect

replace (
    github.com/go-telegram-bot-api/telegram-bot-api/v5 v5.5.1 => github.com/go-telegram-bot-api/telegram-bot-api/v5 v5.5.1
)

require (
    github.com/go-telegram-bot-api/telegram-bot-api/v5 v5.5.1 h1:wG8n/XJQ07TmjbITcGiUaOtXxdrINDz1b0J1w0SzqDc=
    github.com/go-telegram-bot-api/telegram-bot-api/v5 v5.5.1/go.mod h1:A2S0CWkNylc2phvKXWBBdD3K0iGnDBGbzRpISP2zBl8=
)

Running the Bot

To run the bot, you need to have a Telegram bot token. You can get it by talking to BotFather on Telegram.

Create a file named main.go in the project directory with the content from “part2.go”:



How to Use

	1.	Replace TOKEN in the code with your actual Telegram bot token.
	2.	Build and run the bot using:

go run main.go


	3.	Start a chat with your bot on Telegram and send the /start command.
	4.	Follow the prompts to enter your scores for RK1 and RK2.

README now includes all the necessary information to set up, run, and use the Telegram bot.
