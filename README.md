print("Блокировки и обработка ошибок")
import threading
from time import sleep
from random import randint

#from   import  Lock



class Bank:
    def __init__(self):
        self.balance = 0.00 # на входе денег нет.
        self.name = "SuperBank Corp"
        self.lock = threading.Lock()



    def deposit(self):
        for i in range ( 100):
            new_summ =  randint(500000, 5000000) / 10000 # валюта учитывается до 4-х знаков
            self.balance += new_summ
            print(f'{i}. Пополнение. Запрос на <случайное число>')
            if self.balance >= 500 and  self.lock.locked():
                self.lock.release()
            sleep(0.001) # имитация задержки транзакции

            print(f"Пополнение: {new_summ:.4f}. Баланс: {self.balance:.4f}.")

    def take(self):
        for j in range ( 100):
            new_summ =  randint(500000, 5000000) / 10000 # валюта учитывается до 4-х знаков
            print(f"{j}. Снятие. Запрос на <случайное число>")
            if new_summ >= self.balance:
                print(f"Запрос отклонён, недостаточно средств. К списанию {new_summ}. Баланс: {self.balance}")
                self.lock.acquire()
            else:
                self.balance -= new_summ
                print(f"Снятие {new_summ:.4f}. Баланс:  {self.balance:.4f}")
                sleep(0.001)



bk = Bank()



# Т.к. методы принимают self, в потоки нужно передать сам объект класса Bank
th1 = threading.Thread(target=Bank.deposit, args=(bk,))
th2 = threading.Thread(target=Bank.take, args=(bk,))

th1.start()
th2.start()
th1.join()
th2.join()

print(f'Итоговый баланс: {bk.balance}')
