from fpdf import FPDF
from m import HealthCheck
import requests
import json
import cowsay
from tabulate import tabulate
import traceback
def valid_input(type, is_integer, min, max):
    while True:
        try:
            value = float(input(f'Enter your {type}: ')) if not is_integer else int(input(f'Enter your {type}: '))
            if min <= value <= max:
                return value
            else:
                print(f'{type} should be between {min} and {max}. \nPlease try again..')
        except ValueError:
            print('Invalid input. Enter again')

def get_user_information():
    weight = valid_input('Weight(kg)', False, 0, 200)
    height = valid_input('Height(cm)', False, 130, 230)
    age = valid_input('Age', True, 0, 200)

    while True:
        sex = input("Enter 1 for Male or 2 for Female: ")
        if sex == '1':
            sex = 'male'
            break
        elif sex == '2':
            sex = 'female'
            break

    check = input(f"Successfully obtained your information:\nWeight: {weight} kg\nHeight: {height} cm\nAge: {age} years\nSex: {sex}\nAre they precise? (Y/n)").lower()
    if check == 'y':
        return weight, height, sex, age
    else:
        print("Failed to obtain accurate information. Let's try again.")
        return get_user_information()

class HealthCheck():
    def __init__(self, w, h, s, a):
        self.w = w
        self.h = h
        self.s = s
        self.a = a


    def bmi(self):
        headers = {
	"X-RapidAPI-Key": "e636fb2b64msh7285249df857ef6p100b6ajsnf72cdb1f6353",
	"X-RapidAPI-Host": "body-mass-index-bmi-calculator.p.rapidapi.com"
}
        url = "https://body-mass-index-bmi-calculator.p.rapidapi.com/metric"
        infor = {"weight": self.w, "height": self.h / 100}
        try:
            response = requests.get(url, headers=headers, params=infor)
            bmi_data = json.loads(response.text)
            print(bmi_data)
            bmi = bmi_data['bmi']
            print(f'Your bmi is: {bmi}')
            if bmi < 18.5:
                print('You are underweight.')
            elif 18.5 <= bmi < 24.9:
                print('Your weight is normal.')
            elif 25 <= bmi < 29.9:
                print('You are overweight.')
            else:
                print('You are obese.')
        except requests.RequestException as e:
            print(f'Failed to retrieve information:{e}')

    def CaloriePerDay(self):
        def activity_level():
            print('\nChoose your activity level: (enter number only)\n')
            print('level 1: "Sedentary: little or no exercise"')
            print('level 2: "Exercise 1-3 times/week"')
            print('level 3: "Exercise 4-5 times/week"')
            print('level 4: "Daily exercise or intense exercise 3-4 times/week"')
            print('level 5: "Intense exercise 6-7 times/week"')
            print('level 6: "Very intense exercise daily, or physical job"')

            active = 'level_' + input("Your activity level: ").lower()
            return active
        url= 'https://fitness-calculator.p.rapidapi.com/dailycalorie'
        active=activity_level()
        info = {
            "age": self.a,
            "gender": self.s,
            "height": self.h,
            "weight": self.w,
            "activitylevel": active
        }
        headers = {
            "X-RapidAPI-Key": "e636fb2b64msh7285249df857ef6p100b6ajsnf72cdb1f6353",
            "X-RapidAPI-Host": "fitness-calculator.p.rapidapi.com"
        }
        try:
            response = requests.get(url, headers=headers, params=info)
            dic_bmr = response.json()
            self.bmr=dic_bmr['data']['BMR']
            print('BMI: the calorie to maintain your energy:',self.bmr)
        except Exception as e:
            print(f"An error occurred: {str(e)}")
            traceback.print_exc()

    def IBW(self):
        #the ideal weight base on your current weight, height and sex IBW
        url_ibw = "https://mega-fitness-calculator1.p.rapidapi.com/ibw"
        headers_ibw = {
	"X-RapidAPI-Key": "e636fb2b64msh7285249df857ef6p100b6ajsnf72cdb1f6353",
	"X-RapidAPI-Host": "mega-fitness-calculator1.p.rapidapi.com"
}
        infor_ibw= {'gender': self.s, 'height': self.h,'weight':self.w}

        try:
            response = requests.get(url_ibw, headers=headers_ibw, params=infor_ibw)
            dic_ibw = response.json()
            self.ibw=dic_ibw['info']['robinson']
            flattened_data = [] #List has append method and dic doesnt
            for key, nested_value in dic_ibw.items():
                for name, weight in nested_value.items():
                    flattened_data.append({'name': name, 'weight': weight})
            table=tabulate(flattened_data, headers="keys", tablefmt="fancy_grid")
            print('Your ideal weight base on your informaion')
            print(table)
        except Exception as e:
            print(f"An error occurred: {str(e)}")
            traceback.print_exc()


    def time(self):
        cowsay.turtle(f"\nwith that being said, if you want to loose weight, follow \n sustainable plan deficit of around 10-20% of your total daily calorie  ")
        cowsay.fox('\nbe patient, i am calculating time to turn your dream come true...')
        self.min = self.bmr* 0.8
        self.max = self.bmr * 0.9
        print(f'daily Calories range: {int(self.min)}-{int(self.max)}')
        time1 = int((self.w -self.ibw ) * 7700 / (0.1 * self.bmr))
        #totalday = (surplus kg)*7700(calo/kg)/ (daily deficit calorie)
        time2 = int((self.w - self.ibw) * 7700 / (0.2 * self.bmr))
        print(f'In {time1}-{time2} days, you will reach your dream weight if keep your calory consumption in range')

    def mealplan(self):
        type = input("What kind of meal would you like? ").lower()
        while True:
            option=input("Hey, I will give you the title and you in charge of find it on you own\n or you feel lazy? i will throw full version at you bro?\n (title: '1', full: '0')")
            if option =="1" or option =="0":
                break
            cowsay.cow('Hey man, dont mess with me. 1 or 0???')

        print('Getting meal plans...\n')

        url = "https://spoonacular-recipe-food-nutrition-v1.p.rapidapi.com/recipes/complexSearch"
        headers = {
	"X-RapidAPI-Key": "e636fb2b64msh7285249df857ef6p100b6ajsnf72cdb1f6353",
	"X-RapidAPI-Host": "spoonacular-recipe-food-nutrition-v1.p.rapidapi.com"
}
        infor = {'query': type, 'maxCalories': self.max/3}

        try:
            responses = requests.get(url, headers=headers, params=infor)
            recipy_dic = responses.json()
        except Exception  as e:
            print('Error: '+str(e))
        recipes=recipy_dic['results']
        print('Here are your meal plans:\n')
        if option =="1":
            for item in recipys:
                title = item['title']
                print(title)
        else:
            flattened_data = []
            attributes = ['id','title','image','calories']

            # Iterate over each recipe dictionary
            for recipe in recipes:
                recipe_value=[]
                # create list for specific attributes
                for attr in attributes:
                    if attr=='calories':
                        recipe_value.append(recipe["nutrition"]["nutrients"][0]["amount"])
                    else:
                        recipe_value.append(recipe[attr])
                flattened_data.append(recipe_value)
                # Add all the keys of the recipe dictionary to the attributes set
            table = tabulate(flattened_data, headers=attributes, tablefmt="fancy_grid")
            print(table)

    def fridge(self):
        answer=input('\ndo you fancy turning ingridient available to incredible food? (y/n)').lower()
        if answer !='y':
            cowsay.fox('finish, take your time and change your eating habit everyday')
            return
        food = input('What ingredients do you have in your fridge? seperated by comma')
        cowsay.cow('Alright, here you go:')
        try:
            url = "https://spoonacular-recipe-food-nutrition-v1.p.rapidapi.com/recipes/findByIngredients"
            headers = {
        "X-RapidAPI-Key": "e636fb2b64msh7285249df857ef6p100b6ajsnf72cdb1f6353",
        "X-RapidAPI-Host": "spoonacular-recipe-food-nutrition-v1.p.rapidapi.com"
}
            response = requests.get(url, headers=headers, params={'ingredients': food})
            recipy_dict = response.json()
        except Exception  as e:
            print('Error:'+ e)
        try:
            ultimate = []
            attributes = ['id','title','image','usedIngredientCount']
            for recipe in recipy_dict:
                recipe_value=list(recipe[attr] for attr in attributes)
                ultimate.append(recipe_value)
            table=tabulate(ultimate, headers=attributes,tablefmt="fancy_grid")
            print(table)
        except Exception as e:
            traceback.print_exc()
        print('\nEnjoy your delicious meals!')
class Report(FPDF, HealthCheck):
    

def main():

    user = HealthCheck(55,150,'female',25)
    user.bmi()
    user.CaloriePerDay()
    user.IBW()
    user.time()
    user.mealplan()
    user.fridge()

if __name__ == "__main__":
    main()
