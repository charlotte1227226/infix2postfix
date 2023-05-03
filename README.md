/*infix2postfix
infix2postfix(中序轉後序)*/
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
void push(char item);
/* Pop element from char stack */
char pop();
/* Push elemnt to int stack */
void ipush(int item);
/* Pop element from int stack */
int ipop();
typedef enum { lparen, rparen, plus, minus, times, divide, mod, eos, operand } precedence;
// return Token's precendence
precedence getPrecedence(char token);
char operator_to_str(int op);
void check_exp(char x[]);//檢查(檔案裡面的是只用於個位數的計算)(for only 0~9 caculate)
void postfix();
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
int eval_postfix();
main() {
	printf("Please input an infix-expression\n");
	scanf("%s", infix_exp);
	check_exp(infix_exp);
	postfix();  //convert infix expresseion infix_exp[] to postfix expression and put it into postfix_exp[]
	printf("\n%s\n", postfix_exp);
	printf("%d", eval_postfix());
	getch(); // quit if any key is pressed
}

