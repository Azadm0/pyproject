My visual code here:

import tkinter
import math

button_values = [
    ['HEX','DEC','BIN','OCT'], #each list is for one row of buttons
    ['AC','+/','%','÷'],
    ['7','8','9','×'],
    ['4','5','6','-'],
    ['1','2','3','+'],
    ['0','.','√','=']
]
topsymbols = ['HEX','DEC','BIN','OCT']# our top row symbols
rightsymbols = ['÷','×','-','+','='] #the right column
greyy = "#999E97"
darkgrey = "#545454"
orange = "#e55c26"
wwhite = "#ffffff"
window = tkinter.Tk()
window.title('pyproject')
window.resizable(False, False)

frame = tkinter.Frame(window)
frame.pack()

label = tkinter.Label(
    frame, text="0", font=("Arial",45),
    background=greyy, foreground=wwhite,
    anchor='e', width=4
) #label is for our result and numbers,and we will use label['text'] as our value, which currently is 0
label.grid(row=0, column=0, columnspan=4, sticky='we')

first_number = None 
operator = None 
base = 'DEC' 
def reset(): #this function is for resetting our value to zero after each calculation(AC)
    global first_number, operator, base
    first_number = None
    operator = None
    base = 'DEC'
    label['text'] = '0'

def format_number(n):
    if n % 1 == 0: #i first made our numbers to float just incase we have a decimal in our num, we r checking here whether out num is integer, if so we r making it an integer then str
        return str(int(n))
    return str(n) #otherwise float will stay as float and will be displayed as decimal

def valid_dec(text):
    if text == '' or text == '-' or text == '.':
        return False
    if text.count('.') > 1:
        return False
    if text.startswith('-'):
        text = text[1:]
    return text.replace('.', '', 1)
 #we are replacing our . with an empty string so that we can compute and return it back

def valbin(text): #if its a valid binary number
    if text == '':
        return False
    for c in text:
        if c not in '01':
            return False
    return True

def valoct(text): #similarly to binary
    if text == '':
        return False
    for c in text:
        if c not in '01234567':
            return False
    return True
def valhex(text):
    if text == '':
        return False
    for c in text:
        if c not in '0123456789ABCDEFabcdef':
            return False
    return True


def button_clicked(value):
    global first_number, operator, base #this is for do actual calculation, like our prog will run after we press each button

    if value in '0123456789':
        if label['text'] == '0': #if 0 is on the screen, we have to switch the 0 to the number we pressed then do the calculation
            label['text'] = value
        else:
            label['text'] += value #if its not 0, we are just appending the digits 

    elif value == '.':
        if '.' not in label['text']:
            label['text'] += '.'
    elif value == 'AC': 
        reset()
    elif value == '+/':
        if valid_dec(label['text']): 
            label['text'] = format_number(float(label['text']) * -1)

    elif value == '%': 
        if valid_dec(label['text']):
            label['text'] = format_number(float(label['text']) / 100)

    elif value == '√': 
        if base != 'DEC' or not valid_dec(label['text']):
            label['text'] = 'Error'
            return
        num = float(label['text'])
        if num < 0:
            label['text'] = 'Error'
            return
        label['text'] = format_number(math.sqrt(num)) #using format_number will help us to remove not needed decimals and display it as a whole number

    elif value in '+-×÷':
        if valid_dec(label['text']):
            first_number = float(label['text'])
            operator = value
            label['text'] = '0'

    elif value == '=':
        if first_number is None or operator is None:
            return
        if not valid_dec(label['text']):
            label['text'] = 'Error'
            return
        second = float(label['text'])
        if operator == '+':
            result = first_number + second
        elif operator == '-':
            result = first_number - second
        elif operator == '×':
            result = first_number * second
        elif operator == '÷':
            result = first_number / second

        label['text'] = format_number(result)
        first_number = None
        operator = None
    elif value in topsymbols:
        text = label['text']
        if base == 'DEC' and valid_dec(text):
            num = int(float(text))
        elif base == 'BIN' and valbin(text):
            num = int(text, 2)
        elif base == 'OCT' and valoct(text):
            num = int(text, 8)
        elif base == 'HEX' and valhex(text):
            num = int(text, 16)
        else:
            label['text'] = 'Error'
            return
        if value == 'BIN':
            label['text'] = bin(num)[2:]
            base = 'BIN'
        elif value == 'HEX':
            label['text'] = hex(num)[2:]
            base = 'HEX'
        elif value == 'OCT':
            label['text'] = oct(num)[2:]
            base = 'OCT'
        elif value == 'DEC':
            label['text'] = str(num)
            base = 'DEC'

for r in range(len(button_values)):
    for c in range(4):
        v = button_values[r][c]
        b = tkinter.Button(
            frame, text=v, font=("Arial",30),
            width=3, height=1,
             command=lambda x=v: button_clicked(x))
        b.grid(row=r+1, column=c) #for displaying our buttons, r+1 , the 1 is because on the top its our calculation result , we cant include it
        if v in topsymbols:
            b.config(background=greyy, foreground=wwhite) #config is helping to design our button section
        elif v in rightsymbols:
            b.config(background=orange, foreground=wwhite)
        else:
            b.config(background=darkgrey, foreground=wwhite)
window.mainloop()
