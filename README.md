/*infix2postfix
infix2postfix(中序轉後序)
*/

/* The author of this software is Kuan Jen Lin, FJU-EE.  Copyright (c) 2018 by
 * Kuan Jen Lin. The program was designed for "Data structure" course.
 */
 /*************** Notes  ******************************************/
 /* Stack contains character, unlike texbook program, which contains precedence item */
 /* token is a character, not a precedence item */
 /* use getPrecedence() to get token's precedence */
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#define MAX_STACK_SIZE 101
/* in-stack precedence */
int isp[] = { 0, 19, 12, 12, 13, 13, 13, 0 };
/* incoming precedence  */
int icp[] = { 20, 19, 12, 12, 13, 13, 13, 0 };

/* global variables */
char infix_exp[MAX_STACK_SIZE] = "\0";
char postfix_exp[MAX_STACK_SIZE] = "\0";

/* global stacks */
char Stack[MAX_STACK_SIZE];
int iStack[MAX_STACK_SIZE];
int Top = -1;
int iTop = -1;

/* Push elemnt to char stack */
void push(char item) {
	if (Top >= MAX_STACK_SIZE - 1) {
		printf("STACK is full\n");
		exit(1);
	}
	Stack[++Top] = item;
}
/* Pop element from char stack */
char pop() {
	if (Top == -1) {
		printf("STACK is empty\n");
		exit(1);
	}
	return Stack[Top--];
}
/* Push elemnt to int stack */
void ipush(int item) {
	if (iTop >= MAX_STACK_SIZE - 1) {
		printf("STACK is full\n");
		exit(1);
	}
	iStack[++iTop] = item;
}
/* Pop element from int stack */
int ipop() {
	if (iTop == -1) {
		printf("STACK is empty\n");
		exit(1);
	}
	return iStack[iTop--];
}

typedef enum { lparen, rparen, plus, minus, times, divide, mod, eos, operand } precedence;
// return Token's precendence
precedence getPrecedence(char token) {
	switch (token) {
	case  '(': return lparen;
	case  ')': return rparen;
	case  '+': return plus;
	case  '-': return minus;
	case  '/': return divide;
	case  '*': return times;
	case  '%': return mod;
	case  '\0': return eos;
	default: return operand;
	}
}
char operator_to_str(int op) {
	switch (op) {
	case plus:
		return '+';
	case minus:
		return '-';
	case times:
		return '*';
	case divide:
		return '/';
	case mod:
		return '%';
	default:
		return '?';
	}
}

void check_exp(char x[]) {
	int i = 0;
	int sdigit = 0;
	while (x[i] != '\0') {
		if (x[i] >= '0' && x[i] <= '9')
			if (sdigit) { printf("Cannot accept double-digit operand\n"); exit(1); }
			else sdigit = 1;
		else if (x[i] == '(' || x[i] == ')' || x[i] == '+' ||
			x[i] == '-' || x[i] == '*' || x[i] == '/' || x[i] == '%')
			sdigit = 0;
		else {
			printf("Error input expression\n");
			exit(1);
		}
		i++;
	}
}
void postfix() {
	int i = 0, j = 0;
	char token, temp;

	push(eos);//把'\0'push進stack裡面

	while ((token = infix_exp[i++]) != '\0') {
		switch (getPrecedence(token)) {
		case operand:
			postfix_exp[j++] = token;//把數字(字串)弄進postfix_exp j++(postfix_exp往j的下一個位置前進)
			break;
		case rparen:
			while (Stack[Top] != lparen) {
				postfix_exp[j++] = operator_to_str(pop());
				//只要istack的最上面不是左括弧 把stack的頂的位置pop出來放進postfix_exp的j的位置
			}
			pop(); //把左括弧拿掉
			break;
		default:
			while (isp[Stack[Top]] >= icp[getPrecedence(token)]) {//比較stack頂端運算子的優先順序和目前 token 的優先順序
				postfix_exp[j++] = operator_to_str(pop());
			}
			/*
			若堆疊頂端運算子的優先順序比較高，就把堆疊頂端運算子pop出來
			並將其放入postfix_exp，重複這個步驟直到堆疊頂端運算子的優先順序比目前 token 的優先順序還要低。最後，把目前的 token 推入堆疊中。
			*/
			push(getPrecedence(token));
			break;
		}
	}
	while ((temp = pop()) != eos) {
		postfix_exp[j++] = operator_to_str(temp);//只要pop出來的不是eos就一直執行
	}
	postfix_exp[j] = '\0';//跳脫迴圈
}

/*
這個函數需要實現將中綴表達式(infix_exp)轉換為後綴表達式(postfix_exp)。按照以下步驟實現該函數：

初始化 Stack 和 postfix_exp。
循環遍歷中綴表達式中的每一個元素(token)：
a. 如果 token 是操作數(operand)，則將其附加到 postfix_exp 的末尾。
b. 如果 token 是左括號(lparen)，則將其壓入 Stack。
c. 如果 token 是右括號(rparen)，則從 Stack 中彈出元素，並將其附加到 postfix_exp 直到彈出的元素是左括號。
d. 如果 token 是運算符(operator)，則重複執行以下步驟直到 Stack 是空的，或者Stack頂元素的優先級小於等於该運算符的優先級：
i. 從 Stack 中彈出元素，並將其附加到 postfix_exp 的末尾。
e. 將 token 壓入 Stack。
從 Stack 中彈出所有剩餘的元素，並將其附加到 postfix_exp 的末尾。
在 postfix_exp 的末尾添加空字符('\0')。
*/

int eval_postfix() {
	int i = 0, op1, op2;
	char token;

	while ((token = postfix_exp[i++]) != '\0') {
		if (getPrecedence(token)==operand) {//token轉換成數字 那個數字代表operand的話
			ipush(token - '0');//轉換成數字
		}
		else {
			op2 = ipop();
			op1 = ipop();
			switch (getPrecedence(token)) {//token轉換成數字
			case plus://數字==plus
				ipush(op1 + op2);//相加
				break;
			case minus://數字==minus
				ipush(op1 - op2);
				break;
			case times://數字==times
				ipush(op1 * op2);
				break;
			case divide://數字==divide
				ipush(op1 / op2);
				break;
			case mod://數字==mod
				ipush(op1 % op2);
				break;
			default:
				printf("wromg operator encountered.\n");
				return -1;
			}
		}
	}
	return ipop();//結果pop出來
}


main() {
	printf("Please input an infix-expression\n");
	scanf("%s", infix_exp);
	check_exp(infix_exp);
	postfix();  //convert infix expresseion infix_exp[] to postfix expression and put it into postfix_exp[]
	printf("\n%s\n", postfix_exp);

	printf("%d", eval_postfix());


	getch(); // quit if any key is pressed
}

