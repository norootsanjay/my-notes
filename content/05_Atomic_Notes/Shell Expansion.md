![[Pasted image 20250808212503.png]]
Let's say we got a folder and the filename of the first file was _echo_, then `*` is a valid command.![[Screenshot 2025-08-08 at 9.34.39 PM.png]]
- Try to refer to filenames in the same directory as ./file.txt
- And, for the quotes:
	Always use the quoting style that is as restrictive as possible
	Thus:
		Prefer single quotes: 'hello world'
		If this is not possible, use double quotes:
		echo "hello ${USER}"
		echo 'hello '"${USER}"
	Use no quoting only if there's no ambiguity, or you want all
	expansions to be applied:
	
> [!Example for no ambiguity]
ls -al (would be annoying: 'ls' '-al')
Here we want all expansions:
echo ./*.txt

---

# Brace Expansion

 