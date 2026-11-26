# Experiment 11

## Keystroke input in C, detection and defence

## Aim

To read test keystrokes from a program's own terminal, display their character codes, save sample input to a Word-readable file and examine host controls relevant to keylogging detection.

## Theory

A keyboard event passes through device and operating-system input processing to an application. A program reading standard input receives the input delivered to its own stream. This lab's examples operate on that stream and do not implement system-wide keyboard capture.

In a terminal's canonical mode, input is normally delivered in lines. Disabling `ICANON` permits noncanonical input, and disabling `ECHO` stops the terminal from displaying typed characters automatically. The handout calls this raw mode, but clearing these two flags alone does not configure every setting of a fully raw terminal. The original settings must be restored after the demonstration. The [Linux termios manual](https://man7.org/linux/man-pages/man3/termios.3.html) describes these flags and the additional changes made by `cfmakeraw`.

For the ASCII examples, `h` is 104, `i` is 105, space is 32 and Escape is 27. General text may use a multibyte encoding, so an arbitrary character does not necessarily correspond to a single ASCII byte.

Hardware loggers intercept input through devices, while software loggers abuse input access or application/OS facilities. Access to Linux input devices depends on ownership, groups, ACLs and session policy. Root is not the only possible access path. Detection combines process and device investigation, startup review, network evidence and physical inspection.

## Requirements

An isolated Linux terminal, GCC, standard C headers and POSIX `termios`/`unistd` support. Use short, non-sensitive sample input in the foreground terminal. A native Windows C compiler does not provide the Linux terminal environment used by these programs.

## Code extracted from the PDF

These are transcriptions of the course examples with whitespace normalised. They retain the source behaviour. Review the specific limitations after the listings before using them as runnable examples.

### warmup.c — PDF page 12

```c
#include <stdio.h>
int main(void) {
  int c;
  printf("Type, then Enter:\n");
  while ((c = getchar()) != '\n')   // each char
    printf("key '%c' = %d\n", c, c);
  return 0;
}
```

### keydemo.c — PDF page 15

```c
#include <stdio.h>
#include <termios.h>
#include <unistd.h>

int main(void) {
  struct termios old, raw;
  tcgetattr(STDIN_FILENO, &old);          // save terminal settings
  raw = old;
  raw.c_lflag &= ~(ICANON | ECHO);        // raw mode: no line buffer / echo
  tcsetattr(STDIN_FILENO, TCSANOW, &raw);
  char c;
  while (read(STDIN_FILENO, &c, 1) == 1) {// read ONE key from THIS window
    if (c == 27) break;                   // ESC quits
    printf("key: '%c'  ascii: %d\n", c, c);
    fflush(stdout);
  }
  tcsetattr(STDIN_FILENO, TCSANOW, &old);  // restore the terminal
  return 0;
}
```

### keys_to_word.c — PDF page 21

```c
#include <stdio.h>
#include <termios.h>
#include <unistd.h>

int main(void) {
  struct termios old, raw;
  tcgetattr(STDIN_FILENO, &old);
  raw = old; raw.c_lflag &= ~(ICANON | ECHO);     // raw mode
  tcsetattr(STDIN_FILENO, TCSANOW, &raw);
  char keys[1000]; int n = 0; char c;
  while (read(STDIN_FILENO, &c, 1) == 1) {         // read own keys
    if (c == 27) break;                            // ESC = stop
    keys[n++] = c;                                 // remember each key
  }
  tcsetattr(STDIN_FILENO, TCSANOW, &old);
  FILE *doc = fopen("captured_keys.doc", "w");     // make the Word file
  fprintf(doc, "{\\rtf1\\ansi {\\b Captured Keys}\\par ");
  for (int i = 0; i < n; i++)                       // write keys into it
    keys[i]=='\n' ? fprintf(doc,"\\par ") : fprintf(doc,"%c",keys[i]);
  fprintf(doc, "\\par}"); fclose(doc);
  printf("Saved to captured_keys.doc\n"); return 0;
}
```

### Source-code limitations

- `warmup.c` stops at a newline but omits an `EOF` check. If input ends first, its loop can continue printing EOF values. A bounded loop condition should also test `c != EOF`.
- The terminal examples omit error checks for `tcgetattr` and `tcsetattr`. Their restoration path is reached after normal loop exit, but abrupt termination may leave terminal settings altered.
- `keys_to_word.c` writes through `keys[n++]` without checking capacity. Before this assignment, require `n < sizeof(keys)` or stop collecting. The displayed source is unsuitable for unbounded input.
- The file-writing example does not check `fopen` and does not escape RTF backslashes or braces. A robust adaptation must handle these cases. Its `.doc` filename contains RTF data rather than the binary Word DOC format.

These limitations are documented explicitly so the extracted source is not mistaken for a hardened implementation.

## Commands extracted from the PDF

The setup, compile and execution sequence comes from PDF pages 11, 18 and 23. Run one program at a time in a terminal. For `warmup`, press Enter. For the noncanonical examples, Escape ends normal capture.

```bash
gcc --version
nano warmup.c
gcc warmup.c -o warmup
./warmup
gcc keydemo.c -o keydemo
./keydemo
gcc keys_to_word.c -o keys_to_word
./keys_to_word
```

The PDF's last opening command uses `Libreoffice filename.doc`. The executable case and example filename are adapted below to match the generated file on Linux.

```bash
libreoffice captured_keys.doc
```

### Device permissions and detection — PDF pages 20 and 25

Inspect permissions before the source's unprivileged read attempt. If access is permitted, the read can expose real device input, so the permission-denial demonstration requires an ordinary account without access. The sample input device may not exist in every VM or WSL environment.

```bash
ls -l /dev/input/event0
cat /dev/input/event0
```

```bash
ps aux | grep -i log
sudo fuser -v /dev/input/event*
ls ~/.config/autostart
systemctl list-unit-files --state=enabled
lsof | grep /dev/input
```

## Procedure and observations

1. Compare the source programs and identify input, processing, output and cleanup operations.
2. Address the listed bounds and error-handling limitations before extending the demonstrations.
3. Enter `hi` in the warm-up and compare the character codes with the source example.
4. In `keydemo`, observe immediate input and exit with Escape. Confirm the terminal's normal echo is restored.
5. For the file demonstration, enter a short ASCII sample and inspect the resulting RTF in an office reader.
6. Review device permissions, processes and startup entries. Treat an input-device reader as a lead to investigate because legitimate desktop services also read input.

## Defence and result

Least privilege, patched software, trusted application sources, endpoint monitoring, MFA and physical-device checks reduce exposure. MFA adds protection but can itself be affected by a compromised endpoint or session theft. An on-screen keyboard is not a general guarantee against capture.

The experiment explains foreground terminal input and connects it to the risks of broader input access. Record observed codes, file contents and cleanup behaviour from the actual run.

## Viva questions

- What changes when `ICANON` and `ECHO` are cleared?
- Why must the keystroke array have a bounds check?
- Why is the generated `.doc` actually RTF?
- Why is an input-device reader not automatically malicious?

## Sources

`Keylogging_Lab_Experiment.pdf`, PDF pages 2–31. Code appears on PDF pages 12, 15 and 21, corresponding to printed slide labels 13/33, 16/33 and 22/33. Existing practical evidence is in [Experiment 11.pdf](<Experiment 11.pdf>).
