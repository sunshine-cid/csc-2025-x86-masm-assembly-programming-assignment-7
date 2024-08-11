; Student
; Professor
; Class: CSC 2025 XXX
; Week 7 - Programming Homework #7
; Date
; Interactive program reads a string, removes anything but letters, and then outputs the string. There's an option to reepeat the process.

INCLUDE C:\Irvine\Irvine32.inc
INCLUDELIB C:\Irvine\Irvine32.lib

.data
	
	; Set the max length for our read string and reserve memory space for the string
	maxLength = 100
	stringToEdit BYTE MaxLength+1 DUP (0)

	; Set several different message strings we'll be using in the program
	msgInstruction BYTE "Please enter a one line string with a maximum of 100 characters: ",0
	msgOriginal BYTE "Original String: ",0
	msgCompressed BYTE "Compressed String: ",0
	msgAgain BYTE "Would you like to enter a new string (y/n)? ",0

	; Set some single-byte character variables for use in comparisons later
	strY BYTE "Y"
	strN BYTE "N"
	strA BYTE "A"
	strZ BYTE "Z"
	strALower BYTE "a"
	strZLower BYTE "z"

	; Reserve memory space for our 'play again'-style pre-exit question
	charAgain BYTE ?

.code

;-------------------------------- MoveDown Procedure 
;	Functional Details: This procedure recieves a pointer to a string and moves 
;	all the other characters in the string down 1 byte
;	Outputs: There is no strict output, but this does modify the string pointed 
;	to by the ESI OFFSET.
;	Registers:	ESI points to the string's current letter and moves through 
;				each other position to the end.
;				EAX (AL) is used to make a comparison to determine if we're at 
;				the end of the string.
;				EBX (BL) is used to grb the next value (ESI+1) and move it into 
;				the current position
;	Memory Locations: No specific memory locations are used but we do reference 
;	stringToEdit's OFFSET through ESI
MoveDown PROC USES ESI EAX EBX
	
MDLoop:
	
	; Grab the value from the current location
	mov al, [esi]
	movsx eax, al ; Sign extend EAX from AL to avoid problems
	
	; Check to see if string is null terminated or empty, and if so exit
	cmp eax, 0
	je MDExit

	mov bl, [esi] + 1 ; Take the value from ESI+1 and put it in BL
	movsx ebx, bl ; Sign extend EBX from BL to avoid problems
	mov [esi], bl ; Move the value we saved into the current ESI position
	inc esi ; Increment up the string

	jmp MDLoop ; Repeat

MDExit:

	ret
MoveDown ENDP

;--------------------------------  CompressString Procedure 
;	Functional Details: Practically this checks a string (offset sent through 
;	ESI) for characters in the range A-Z and a-z. Anything in that range it 
;	keeps, outsiode of that range is overwritten by the next value in ESI.
;	Outputs: No strict outrput is made, but stringToEdit is modified though 
;	references to it's offset in ESI
;	Registers:	EAX (AL) is used to hald the current offset value
;				ESI is used to hold the offset to stringToEdit
;	Memory Locations: This procedure references our string comparison 
;	predefined memory operands strA, strZ, strALower, and strZLower. And 
;	although we don't reference it directly we are accessing stringToEdit 
;	through its offset.
CompressString PROC USES ESI EAX

CSLoop:
	
	mov al, [esi]
	movsx eax, al ; Sign extend AL into EAX to prevent any sign-based problems
	; Check to see if value at current string position is null terminated or empty, and if so exit
	cmp eax, 0
	je CSExit

CSLevel1: ; This section compares and strips out characters below ASCII values of "A" or "z"

	; Compare EAX to "A" if below, compress
	cmp al, strA
	jb CSCompress

	; Compare EAX to "z" if above, compress
	cmp al, strZLower
	ja CSCompress

CSLevel2: ; This section compares and skips values below ASCII values of "Z" or "a"

	; Compare EAX to "Z" if below or equal, skip
	cmp al, strZ
	jbe CSNext

	; Compare EAX to "a" if above or equal, skip
	cmp al, strZ
	jae CSNext
	; Otherwise, compress

CSCompress: ; This section calls the MoveDown function to erase the current value at ESI with the values above it
	
	Call MoveDown
	; We don't need to incriment becasue MoveDown essentially does that for us
	jmp CSLoop

CSNext: ; This section just increments ESI, essentially moving us on to our next letter
	
	inc esi
	jmp CSLoop

CSExit:

	ret
CompressString ENDP

;-------------------------------- Main Procedure 
;	Functional Details: Practically this procedure informs the user we'll be 
;	taking in a string, takes in the string, displays it, calls to edit it, 
;	displays the edited string, and finally asks the user if they'd like to do 
;	it all again.
;	Outputs: We display a message asking for string input, the string is 
;	displayed while being typed, the string is again displayed whit an 
;	"Original String: " label, then the edited string is displayed with a 
;	"Compressed String: " label, then a message asking if the user would like 
;	to enter another string is displayed.
;	Registers:	EDX is used to reference several string memory offsets.
;				ECX is used to set the max length of a ReadString call's input
;				ESI is used to poutn to the input string in order that it might 
;				be edited though the CompressString and MoveDown procedures.
;	Memory Locations: stringToEdit is specifically used to store the user 
;	entered string. charAgain is explicitly used to hold the value (Y or N) 
;	for if the user wants to go again. There are also many string offsets used 
;	to display various messages.
main PROC
	
MainLoopStart:
	;Dislpay the welcome message
	mov edx, OFFSET msgInstruction
	call WriteString
	call Crlf ; Drop down a line for formatting purposes

	; Take input of the string
	mov edx, OFFSET stringToEdit
	mov ecx, maxLength
	call ReadString

	; Display the "Original String" message
	mov edx, OFFSET msgOriginal
	call WriteString
	mov edx, OFFSET stringToEdit
	call WriteString
	call Crlf ; New line for readability

	; Edit the string
	mov esi, OFFSET stringToEdit
	call CompressString

	; Display the "Compressed String" message and the edited string
	mov edx, OFFSET msgCompressed
	call WriteString
	mov edx, OFFSET stringToEdit
	call WriteString
	call Crlf ; New line for readability
	call Crlf ; New line for readability
	
MainAgain: ; "Would you like to enter a new string?" portion of the program
	
	mov  edx,OFFSET msgAgain
	call WriteString

	call ReadChar
	call WriteChar ; Display the character typed, this is necessary since ReadChar doesn't display the Char typed
	call Crlf ; Move the display line down 1
	call Crlf ; Move the display line down 1

	movsx eax, al ; we need to overwrite the rest of the EAX register with the sign from AL becasue ReadChar loads the value to AL
	mov charAgain, al ; Store the read character in our memory operand

	; Convert input Char to Uppercase
	INVOKE Str_ucase, ADDR charAgain
	
	; Compare input character to uppercase Y, if equals jump to MainLoopStart
	mov al, charAgain
	cmp al, strY
	je MainLoopStart

	; Compare input to uppercase N, if equals, jump to MainExit
	cmp al, strN
	je MainExit

	; If neither y or n was pressed, repeat prompt
	jmp MainAgain

MainExit:

    ; Call Irvine's exit procedure
	exit
main ENDP
END main
