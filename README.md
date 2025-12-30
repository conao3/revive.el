# revive.el

Save and restore your Emacs session state, including window configurations that other session managers cannot preserve.

## Overview

revive.el saves your current Emacs editing environment to a file and restores it later. Unlike `desktop.el` or `saveconf.el`, it captures and reconstructs window splitting configurations exactly as you left them.

## Features

- Save and restore complete window layouts
- Preserve buffer positions and marks
- Store global and local variable values
- Support for special buffers (dired, shell, info, mh-e, mew, gnus, and more)
- Multiple configuration slots for different workflows
- Programmable API for custom integrations

## Installation

Place `revive.el` in your Emacs load path and add the following to your init file:

```elisp
(autoload 'save-current-configuration "revive" "Save status" t)
(autoload 'resume "revive" "Resume Emacs" t)
(autoload 'wipe "revive" "Wipe Emacs" t)
```

Then bind keys to these functions:

```elisp
(define-key ctl-x-map "S" 'save-current-configuration)
(define-key ctl-x-map "F" 'resume)
(define-key ctl-x-map "K" 'wipe)
```

If you are using `windows.el`, these autoload and key binding configurations are handled automatically.

## Usage

### Basic Commands

| Command | Default Key | Description |
|---------|-------------|-------------|
| `save-current-configuration` | `C-x S` | Save current window and buffer state |
| `resume` | `C-x F` | Restore previously saved state |
| `wipe` | `C-x K` | Close all buffers (prompts to save) |

### Multiple Configurations

Use a numeric prefix to save or load different configuration slots:

```
C-u 2 C-x S    Save to slot 2
C-u 2 C-x F    Load from slot 2
```

## Configuration

### Saving Variables

By default, revive.el saves common global and local variables. To save additional variables, customize these lists:

```elisp
;; Global variables
(setq revive:save-variables-global-private
      '(file-history buffer-history minibuffer-history))

;; Local variables (buffer-specific)
(setq revive:save-variables-local-private
      '(my-custom-variable))
```

### Restoring Special Buffers

For buffers that require special handling (modes without associated files), add entries to the mode-command alist:

```elisp
(setq revive:major-mode-command-alist-private
      '((your-mode . your-mode-command)
        ("*Buffer Name*" . buffer-restore-function)))
```

The alist maps either a major mode symbol or a buffer name string to the command that should recreate the buffer.

### Ignoring Buffers

To prevent certain buffers from being saved:

```elisp
(setq revive:ignore-buffer-pattern "^ \\*\\|^\\*scratch\\*")
```

### Configuration File Location

By default, configurations are saved to `~/.revive.el`. To change this:

```elisp
(setq revive:configuration-file "~/path/to/your/revive-config.el")
```

## Programmatic Use

revive.el provides two functions for use in other programs:

```elisp
;; Save window configuration to a printable format
(current-window-configuration-printable)

;; Restore from saved configuration
(restore-window-configuration config)
```

Example for copying window configuration between frames:

```elisp
(defun frame-copy-configuration (nth)
  "Copy configuration of current frame to NTH next frame."
  (let ((config (current-window-configuration-printable)))
    (other-frame nth)
    (restore-window-configuration config)))
```

## Compatibility

revive.el works with GNU Emacs 19 and later, including XEmacs.

## License

This software is distributed as free software. The author is not responsible for any issues caused by this software.

## Author

HIROSE Yuuji (yuuji@gentei.org)

Original version: 1994
