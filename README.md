# OSlabs
Лабораторные работы студента 2261-ДБ Медведева Владислава Александровича по дисциплине "Операционные системы"

# Лабораторная работа №1

1. Создаем заголовочный файл для работы функции

<img width="447" height="27" alt="Screenshot 2026-05-16 191147" src="https://github.com/user-attachments/assets/c49877df-42d4-45df-9b14-e86675a4eaf9" />

2. Пишем саму функцию

<img width="453" height="216" alt="Screenshot 2026-05-16 191133" src="https://github.com/user-attachments/assets/4e02b969-f096-4b48-ae90-28863d64aa9c" />

3. Тестируем функцию

<img width="404" height="197" alt="Screenshot 2026-05-16 191109" src="https://github.com/user-attachments/assets/f1bc5147-42e8-4e6d-a680-6df3894192c0" />

4. Компилируем оба файла (с 2 и 3 пунктов) и смотрим результат

<img width="499" height="112" alt="Screenshot 2026-05-16 191202" src="https://github.com/user-attachments/assets/78bbda43-7ed4-45e7-9110-15e7573a509d" />

5. Выполняем компиляцию в ассемблерный код и расписываем его. Оптимизация нулевая.

<img width="569" height="39" alt="image" src="https://github.com/user-attachments/assets/687997ce-7e59-452a-b8f8-b5089b758041" />

```asm
.file	"factorial.cpp"	                            # имя исходного файла
.text	                                            # начало секции машинного кода
.globl	_Z9factoriali	                            # сделать функцию доступной компоновщику
.def	_Z9factoriali; .scl 2; .type 32; .endef     # служебная информация о функции

.seh_proc	_Z9factoriali	    # начало описания функции для Windows SEH
_Z9factoriali:	                # начало функции factorial
.LFB2239:	                    # внутренняя метка компилятора
	pushq	%rbp	            # сохранить старое значение базового указателя стека
	.seh_pushreg	%rbp	    # служебная информация SEH
	movq	%rsp,%rbp	        # установить новый базовый указатель
	.seh_setframe	%rbp,0
	subq	$16,%rsp	        # выделить 16 байт памяти в стеке
	.seh_stackalloc	16
	.seh_endprologue	        # конец пролога функции
	# ===== Инициализация переменных =====
	movl	%ecx,16(%rbp)	    # сохранить параметр n
	movq	$1,-8(%rbp)	        # result=1
	movl	$1,-12(%rbp)	    # i=1
	jmp	.L2	                    # перейти к проверке условия цикла
	# ===== Тело цикла =====
.L3:
	movl	-12(%rbp),%eax	    # eax=i
	cltq	                    # преобразовать int в long long
	movq	-8(%rbp),%rdx	    # rdx=result
	imulq	%rdx,%rax	        # rax=i*result
	movq	%rax,-8(%rbp)	    # result=result*i
	addl	$1,-12(%rbp)	    # i++
	# ===== Проверка условия =====
.L2:
	movl	-12(%rbp),%eax	    # eax=i
	cmpl	16(%rbp),%eax	    # сравнить i и n
	jle	.L3	                    # если i<=n, перейти в тело цикла
	# ===== Возврат результата =====
	movq	-8(%rbp),%rax	    # поместить result в регистр возврата
	addq	$16,%rsp	        # освободить память стека
	popq	%rbp	            # восстановить старый rbp
	ret	                        # завершить функцию
.seh_endproc	# конец описания функции
# ===== Служебные данные библиотеки =====
.section .rdata,"dr"
_ZNSt8__detail30__integer_to_chars_is_unsignedIjEE:
.byte	1
_ZNSt8__detail30__integer_to_chars_is_unsignedImEE:
.byte	1
_ZNSt8__detail30__integer_to_chars_is_unsignedIyEE:
.byte	1
.ident	"GCC: (MinGW-W64 x86_64-ucrt-posix-seh, built by Brecht Sanders, r3) 14.2.0"
# информация о версии компилятора
```

6. Оптимизация третья.

```asm
.file	"factorial.cpp"
	.text
	.p2align 4
	.globl	_Z9factoriali
	.def	_Z9factoriali;	.scl	2;	.type	32;	.endef
	.seh_proc	_Z9factoriali
_Z9factoriali:
.LFB2263:
	.seh_endprologue
	testl	%ecx, %ecx
	jle	.L4
	leal	1(%rcx), %r8d
	andl	$1, %ecx
	movl	$1, %eax
	movl	$1, %edx
	je	.L3
	movl	$2, %eax
	cmpq	%r8, %rax
	je	.L1
	.p2align 5
	.p2align 4
	.p2align 3
.L3:
	imulq	%rax, %rdx
	leaq	1(%rax), %rcx
	addq	$2, %rax
	imulq	%rcx, %rdx
	cmpq	%r8, %rax
	jne	.L3
.L1:
	movq	%rdx, %rax
	ret
	.p2align 4,,10
	.p2align 3
.L4:
	movl	$1, %edx
	movq	%rdx, %rax
	ret
	.seh_endproc
	.ident	"GCC: (MinGW-W64 x86_64-ucrt-posix-seh, built by Brecht Sanders, r3) 14.2.0"
```

7. Создаём Makefile. Тестируем.

<img width="569" height="428" alt="Screenshot 2026-05-16 194011" src="https://github.com/user-attachments/assets/df4e25f6-3487-4fec-8372-c99154da409c" />

<img width="381" height="183" alt="Screenshot 2026-05-16 194052" src="https://github.com/user-attachments/assets/b7f377f1-0815-4633-88ed-3bbcb9b276d8" />

8. Улучшаем программу. Добавляем параллельный поток и синхронизацию.

<img width="791" height="657" alt="Screenshot 2026-05-16 195524" src="https://github.com/user-attachments/assets/a9c799fa-9bd6-4232-ae3f-98f07ec131c9" />

9. Меняем Makefile с учетом изменений из прошлого пункта.

<img width="512" height="343" alt="Screenshot 2026-05-16 195534" src="https://github.com/user-attachments/assets/948fe0dd-02c0-4fae-8f05-c118003eac99" />

10. Тестируем

<img width="498" height="154" alt="Screenshot 2026-05-16 195543" src="https://github.com/user-attachments/assets/f2c27c93-58a2-43d4-a7c4-8f33b463dc6c" />

<img width="296" height="147" alt="Screenshot 2026-05-16 195613" src="https://github.com/user-attachments/assets/c484a657-b37f-4075-a7e8-5dbede5f0d97" />


# Лабораторная работа №2

Посмотрите видео с установкой в файлах (installing_arch.mp4).

# Лабораторная работа №3(А). Вариант 5. В текстовых файлах (. t x t ) заменить одно слово на другое, из найденных файлов составить список, сохранить его в файл.

1. Открываем консоль в Linux.

<img width="736" height="484" alt="Screenshot 2026-05-16 203043" src="https://github.com/user-attachments/assets/ba3fe8c1-d5d9-48ee-99bc-8114a0015c09" />

2. Создаем 4 файла для проверки скрипта.

<img width="731" height="478" alt="Screenshot 2026-05-16 203304" src="https://github.com/user-attachments/assets/d1fa2c91-1118-41b0-b5a9-243a58f8584b" />

3. Открываем самый лучший в мире редактор кода по мнению самых лучших людей (Vim) и записываем сам скрипт.

<img width="689" height="489" alt="Screenshot 2026-05-16 205059" src="https://github.com/user-attachments/assets/fc462b9c-82bf-42a7-bc2e-29c857b7b6a6" />

4. Делаем скрипт исполняемым через chmod +x lab3a.sh. Выполняем скрипт и проверяем.

<img width="729" height="459" alt="Screenshot 2026-05-16 211223" src="https://github.com/user-attachments/assets/bc0319f0-843e-40c7-8b99-8227c5bb9460" />

# Лабораторная работа №3(B). Вариант 5. В текстовых файлах (. t x t ) заменить одно слово на другое, из найденных файлов составить список, сохранить его в файл.

1. Разрешаем выполнение скриптов для текущего юзера (на всякий пожарный). Редактируем файл в самом крутецки крутом редакторе кода всех времен и народов (Vim).

<img width="733" height="47" alt="Screenshot 2026-05-16 212108" src="https://github.com/user-attachments/assets/5a3c1dfd-9826-43d8-945a-c0d6bda5569f" />


<img width="871" height="337" alt="image" src="https://github.com/user-attachments/assets/4a75ee52-c5ef-4fe8-ba5c-fe3cd56b4e07" />

2. Создаем 4 файла для проверки скрипта.

<img width="648" height="83" alt="Screenshot 2026-05-16 212320" src="https://github.com/user-attachments/assets/d8958417-2eab-4c18-a1de-700007d2bf78" />

3. Выполняем скрипт. Смотрим результат.

<img width="538" height="155" alt="Screenshot 2026-05-16 212330" src="https://github.com/user-attachments/assets/6cdb56a3-2263-4030-bb64-aeb53d9d78d0" />
