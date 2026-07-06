"""
TypeStrike - Keyboard Speed Test (Tkinter)
==========================================
No installation needed — Tkinter is built into Python!
Run: python typestrike.py
"""

import tkinter as tk
import random
import time

# ── Paragraphs ────────────────────────────────────────────────────────────────
PARAGRAPHS = [
    "The quick brown fox jumps over the lazy dog near the riverbank where the willow trees sway gently in the breeze.",
    "Technology is reshaping every industry. Artificial intelligence and automation are driving a new era of productivity unlike anything seen before.",
    "In the depths of space, countless stars burn with silent fury. Each one a sun to unknown worlds waiting to be explored by curious minds.",
    "To become a great programmer, one must practice daily, read code written by others, and never stop learning new languages and techniques.",
    "The human brain processes billions of signals every second and stores memories that last a lifetime, making us aware of our own existence.",
    "Speed and accuracy are both critical when mastering the keyboard. Regular practice builds muscle memory and trains your fingers to move with confidence.",
    "Coffee was first discovered in Ethiopia and later spread across the world. By the fifteenth century it fueled intellectual discussions everywhere.",
    "The internet transformed how humanity communicates and learns. In just decades it connected billions of people and changed culture and science forever.",
]

# ── Colors ────────────────────────────────────────────────────────────────────
BG      = "#0a0c10"
SURFACE = "#111318"
BORDER  = "#1e2230"
ACCENT  = "#00e5ff"
ACCENT2 = "#ff3c6e"
ACCENT3 = "#a8ff3e"
YELLOW  = "#f7c948"
TEXT    = "#c8d0e0"
DIM     = "#4a5068"
CORRECT = "#a8ff3e"
WRONG   = "#ff3c6e"


class TypeStrike:
    def __init__(self, root):
        self.root = root
        self.root.title("TypeStrike — Keyboard Speed Test")
        self.root.configure(bg=BG)
        self.root.resizable(False, False)

        self.TIME_LIMIT = 60
        self.timer_job  = None

        self._build_ui()
        self.reset()

    # ── Build UI ──────────────────────────────────────────────────────────────
    def _build_ui(self):
        # Title bar
        title_frame = tk.Frame(self.root, bg=BG)
        title_frame.pack(pady=(18, 0), padx=30, anchor="w")
        tk.Label(title_frame, text="TYPE",   font=("Courier New", 28, "bold"), fg=ACCENT,  bg=BG).pack(side="left")
        tk.Label(title_frame, text="STRIKE", font=("Courier New", 28, "bold"), fg=ACCENT2, bg=BG).pack(side="left")
        tk.Label(title_frame, text="  —  Keyboard Speed Test",
                 font=("Courier New", 12), fg=DIM, bg=BG).pack(side="left", pady=6)

        # Stat cards
        stats_frame = tk.Frame(self.root, bg=BG)
        stats_frame.pack(fill="x", padx=30, pady=(10, 0))

        self.wpm_var  = tk.StringVar(value="0")
        self.acc_var  = tk.StringVar(value="—")
        self.err_var  = tk.StringVar(value="0")
        self.time_var = tk.StringVar(value=str(self.TIME_LIMIT))

        for label, var, color in [
            ("WPM",      self.wpm_var,  ACCENT),
            ("ACCURACY", self.acc_var,  ACCENT3),
            ("ERRORS",   self.err_var,  ACCENT2),
            ("TIME LEFT",self.time_var, YELLOW),
        ]:
            card = tk.Frame(stats_frame, bg=SURFACE,
                            highlightthickness=1, highlightbackground=BORDER)
            card.pack(side="left", expand=True, fill="both", padx=(0, 8), ipady=8, ipadx=12)
            tk.Frame(card, bg=color, height=3).pack(fill="x")
            tk.Label(card, text=label, font=("Courier New", 9),          fg=DIM,   bg=SURFACE).pack(anchor="w", padx=10, pady=(4,0))
            tk.Label(card, textvariable=var, font=("Courier New", 26, "bold"), fg=color, bg=SURFACE).pack(anchor="w", padx=10)

        # Progress bar
        prog_frame = tk.Frame(self.root, bg=BG)
        prog_frame.pack(fill="x", padx=30, pady=(10, 0))
        self.prog_canvas = tk.Canvas(prog_frame, height=5, bg=BORDER, highlightthickness=0, bd=0)
        self.prog_canvas.pack(fill="x")
        self.prog_bar = self.prog_canvas.create_rectangle(0, 0, 0, 5, fill=ACCENT, outline="")

        # Paragraph display
        para_outer = tk.Frame(self.root, bg=BORDER, bd=1)
        para_outer.pack(fill="x", padx=30, pady=(10, 0))
        para_frame = tk.Frame(para_outer, bg=SURFACE, padx=20, pady=16)
        para_frame.pack(fill="x")

        self.para_text = tk.Text(
            para_frame,
            font=("Courier New", 14),
            bg=SURFACE, fg=DIM,
            relief="flat", bd=0,
            wrap="word", height=4,
            cursor="arrow", state="disabled",
            spacing1=4, spacing2=4,
        )
        self.para_text.pack(fill="x")
        self.para_text.tag_config("correct", foreground=CORRECT)
        self.para_text.tag_config("wrong",   foreground=WRONG, underline=True)
        self.para_text.tag_config("pending", foreground=DIM)
        self.para_text.tag_config("cursor",  foreground=TEXT, background="#1a2a2a", underline=True)

        # Hint
        self.hint_var = tk.StringVar(value="Type the passage above — timer starts on first keystroke")
        tk.Label(self.root, textvariable=self.hint_var,
                 font=("Courier New", 10), fg=DIM, bg=BG).pack(pady=(5, 0))

        # Input box
        input_outer = tk.Frame(self.root, bg=BORDER, bd=1)
        input_outer.pack(fill="x", padx=30, pady=(10, 0))
        self.input_var = tk.StringVar()
        self.input_var.trace_add("write", self._on_input_change)
        self.entry = tk.Entry(
            input_outer,
            textvariable=self.input_var,
            font=("Courier New", 14),
            bg="#0d1117", fg=TEXT,
            insertbackground=ACCENT,
            relief="flat", bd=0,
        )
        self.entry.pack(fill="x", ipady=10, ipadx=12)

        # Buttons
        btn_frame = tk.Frame(self.root, bg=BG)
        btn_frame.pack(pady=(14, 10), padx=30, anchor="e")
        tk.Button(btn_frame, text="NEW TEXT", font=("Courier New", 10, "bold"),
                  fg=DIM, bg=SURFACE, activeforeground=ACCENT, activebackground=SURFACE,
                  relief="flat", bd=0, padx=16, pady=7, cursor="hand2",
                  command=self.reset).pack(side="left", padx=(0, 8))
        tk.Button(btn_frame, text="↺  RESTART", font=("Courier New", 10, "bold"),
                  fg=BG, bg=ACCENT, activeforeground=BG, activebackground="#33ecff",
                  relief="flat", bd=0, padx=16, pady=7, cursor="hand2",
                  command=self.reset).pack(side="left")

        # Results panel (shown after test ends)
        self.results_frame = tk.Frame(self.root, bg=SURFACE,
                                      highlightthickness=1, highlightbackground=BORDER)

        self.root.bind("<Tab>", lambda e: self.reset())

    # ── Game Logic ────────────────────────────────────────────────────────────
    def reset(self):
        if self.timer_job:
            self.root.after_cancel(self.timer_job)
            self.timer_job = None

        self.paragraph     = random.choice(PARAGRAPHS)
        self.typed_index   = 0
        self.errors        = 0
        self.error_indices = set()
        self.total_typed   = 0
        self.start_time    = None
        self.running       = False
        self.finished      = False
        self.time_left     = self.TIME_LIMIT

        self.wpm_var.set("0")
        self.acc_var.set("—")
        self.err_var.set("0")
        self.time_var.set(str(self.TIME_LIMIT))

        self._render_paragraph()
        self.input_var.set("")
        self.entry.config(state="normal", bg="#0d1117")
        self.entry.focus()
        self.results_frame.pack_forget()
        self._update_progress(0)
        self.hint_var.set("Type the passage above — timer starts on first keystroke")

    def _render_paragraph(self):
        self.para_text.config(state="normal")
        self.para_text.delete("1.0", "end")
        self.para_text.insert("1.0", self.paragraph)
        for i in range(len(self.paragraph)):
            tag = "cursor" if i == 0 else "pending"
            self.para_text.tag_add(tag, f"1.0+{i}c", f"1.0+{i+1}c")
        self.para_text.config(state="disabled")

    def _update_cursor(self, index):
        self.para_text.config(state="normal")
        self.para_text.tag_remove("cursor", "1.0", "end")
        if index < len(self.paragraph):
            self.para_text.tag_add("cursor", f"1.0+{index}c", f"1.0+{index+1}c")
        self.para_text.config(state="disabled")

    def _on_input_change(self, *args):
        if self.finished:
            return
        typed = self.input_var.get()
        if not typed:
            return

        if not self.running:
            self.running    = True
            self.start_time = time.time()
            self._tick()

        ch = typed[-1]
        self.total_typed += 1

        if self.typed_index < len(self.paragraph):
            expected = self.paragraph[self.typed_index]
            pos, nxt = f"1.0+{self.typed_index}c", f"1.0+{self.typed_index+1}c"

            self.para_text.config(state="normal")
            for tag in ("cursor", "pending", "correct", "wrong"):
                self.para_text.tag_remove(tag, pos, nxt)

            if ch == expected:
                self.para_text.tag_add("correct", pos, nxt)
                self.entry.config(bg="#0d1117")
            else:
                self.para_text.tag_add("wrong", pos, nxt)
                if self.typed_index not in self.error_indices:
                    self.errors += 1
                    self.error_indices.add(self.typed_index)
                self.entry.config(bg="#1a0810")
                self.root.after(300, lambda: self.entry.config(bg="#0d1117"))

            self.para_text.config(state="disabled")
            self.typed_index += 1
            self._update_cursor(self.typed_index)

        self.input_var.set("")
        self._update_stats()
        self._update_progress(self.typed_index / len(self.paragraph))

        if self.typed_index >= len(self.paragraph):
            self._end_game()

    def _tick(self):
        if not self.running or self.finished:
            return
        elapsed = time.time() - self.start_time
        self.time_left = max(0, self.TIME_LIMIT - elapsed)
        self.time_var.set(f"{int(self.time_left)}s")
        if self.time_left <= 0:
            self._end_game()
        else:
            self.timer_job = self.root.after(200, self._tick)

    def _update_stats(self):
        if not self.start_time:
            return
        mins = (time.time() - self.start_time) / 60
        wpm  = int((self.typed_index / 5) / mins) if mins > 0.001 else 0
        self.wpm_var.set(str(wpm))
        self.err_var.set(str(self.errors))
        if self.total_typed > 0:
            acc = int(((self.total_typed - self.errors) / self.total_typed) * 100)
            self.acc_var.set(f"{acc}%")

    def _update_progress(self, fraction):
        self.prog_canvas.update_idletasks()
        w = self.prog_canvas.winfo_width()
        self.prog_canvas.coords(self.prog_bar, 0, 0, w * fraction, 5)

    def _end_game(self):
        if self.finished:
            return
        self.finished = True
        self.running  = False
        if self.timer_job:
            self.root.after_cancel(self.timer_job)

        elapsed = time.time() - self.start_time if self.start_time else 1
        mins    = elapsed / 60
        wpm     = int((self.typed_index / 5) / mins) if mins > 0.001 else 0
        acc     = int(((self.total_typed - self.errors) / self.total_typed) * 100) if self.total_typed else 100

        self.wpm_var.set(str(wpm))
        self.acc_var.set(f"{acc}%")
        self.err_var.set(str(self.errors))
        self._update_progress(1.0)
        self.entry.config(state="disabled")
        self.hint_var.set("Test complete!  Press TAB or click RESTART to play again.")

        if wpm >= 80 and acc >= 95:
            grade, gcolor = "LIGHTNING FAST", ACCENT
        elif wpm >= 60 and acc >= 90:
            grade, gcolor = "BLAZING SPEED",  ACCENT3
        elif wpm >= 40:
            grade, gcolor = "SOLID TYPIST",   YELLOW
        else:
            grade, gcolor = "KEEP PRACTICING", ACCENT2

        # Build results panel
        for w in self.results_frame.winfo_children():
            w.destroy()

        tk.Label(self.results_frame, text="── TEST COMPLETE ──",
                 font=("Courier New", 11), fg=DIM, bg=SURFACE).pack(pady=(16, 4))
        tk.Label(self.results_frame, text=str(wpm),
                 font=("Courier New", 52, "bold"), fg=ACCENT, bg=SURFACE).pack()
        tk.Label(self.results_frame, text="WORDS PER MINUTE",
                 font=("Courier New", 10), fg=DIM, bg=SURFACE).pack(pady=(0, 12))

        sub = tk.Frame(self.results_frame, bg=SURFACE)
        sub.pack(pady=(0, 12))
        for val, lbl, col in [
            (f"{acc}%",          "ACCURACY", ACCENT3),
            (str(self.errors),   "ERRORS",   ACCENT2),
            (f"{int(elapsed)}s", "TIME",     YELLOW),
        ]:
            f = tk.Frame(sub, bg=SURFACE)
            f.pack(side="left", padx=30)
            tk.Label(f, text=val, font=("Courier New", 22, "bold"), fg=col, bg=SURFACE).pack()
            tk.Label(f, text=lbl, font=("Courier New", 9),          fg=DIM, bg=SURFACE).pack()

        tk.Label(self.results_frame, text=grade,
                 font=("Courier New", 13, "bold"), fg=gcolor, bg=SURFACE).pack(pady=(0, 16))

        self.results_frame.pack(fill="x", padx=30, pady=(10, 20))


# ── Entry point ───────────────────────────────────────────────────────────────
if __name__ == "__main__":
    root = tk.Tk()
    root.geometry("880x700")
    app = TypeStrike(root)
    root.mainloop()
