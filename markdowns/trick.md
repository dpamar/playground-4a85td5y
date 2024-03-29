# The trick

Let's suppose our BF interpreter is named `bf`, like the one available in almost all distributions' packages.

Instead of executing `bf myscript.bf`, let's do things differently

* First, locate your bf. `which bf`.
  * Let's say it's /usr/local/bin/bf 
* Then, add a first line at myscript.bf : #!_bf path_
  * e.g. #!/usr/local/bin/bf
* Finally, change permissions on your script. `chmod 755 myscript.bf`

Now, you can execute ./myscript.bf and launch it.

_Note for Windows users:_ you can use any command line BF interpreter and Git Bash (or any other Bash for Windows implementation). However, make sure you renamed your "bf.exe" into "bf" or "bfexe", but, well, remove the dot. It works both with and without the dot, but there is another advantage of dotless interpreter path, described below.

# Advantages

This is really useful, for multiple reasons
* First of all, look at line #!/usr/local/bin/bf
  * Unless you put a dot (or any other BF instruction char) in the path, there is no BF instruction in there
  * This means the script is both executable, and interpretable as is : first line only contains BF comments, that's all
    * So, avoid `bf.exe`, `/lost+found/bf`, `/usr/local/bin/bf-lightning`, ...
* No need to compile the code
* And on some BF implementations, the carriage return is not considered as End Of Input by default
  * `bf myscript.bf` that expects an input will read a single line
  * `./myscript.bf` will read carriage returns as `code 10` char, that's all (and Ctrl-C will "close" the program).

Let's implement an example that leverage this new capability.
