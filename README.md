!pip install colab-x11
import colab_x11
colab_x11.start()
import tkinter as tk
from tkinter import messagebox
import sys
import random

if hasattr(sys.stdout, 'reconfigure'):
    sys.stdout.reconfigure(encoding='utf-8')

HERO_ROLES = {
    # Tanks (16)
    "D.Va": "Tank", "D.Mon": "Tank", "Doomfist": "Tank", "Domina": "Tank", 
    "Hazard": "Tank", "Junker Queen": "Tank", "Mauga": "Tank", "Orisa": "Tank", 
    "Ramattra": "Tank", "Reinhardt": "Tank", "Roadhog": "Tank", "Sigma": "Tank", 
    "Winston": "Tank", "Wrecking Ball": "Tank", "Wuyang": "Tank", "Zarya": "Tank",
    
    # DPS (21)
    "Ashe": "DPS", "Anran": "DPS", "Bastion": "DPS", "Cassidy": "DPS", 
    "Echo": "DPS", "Emri": "DPS", "Enron": "DPS", "Genji": "DPS", 
    "Hanzo": "DPS", "Junkrat": "DPS", "Mei": "DPS", "Pharah": "DPS", 
    "Reaper": "DPS", "Sojourn": "DPS", "Soldier: 76": "DPS", "Sombra": "DPS", 
    "Symmetra": "DPS", "Torbjörn": "DPS", "Tracer": "DPS", "Venture": "DPS", 
    "Widowmaker": "DPS",
    
    # Supports (16)
    "Ana": "Support", "Baptiste": "Support", "Brigitte": "Support", "Freja": "Support",
    "Illari": "Support", "Jetpack Cat": "Support", "Juno": "Support", "Kiriko": "Support", 
    "Lifeweaver": "Support", "Lúcio": "Support", "Mercy": "Support", "Mizuki": "Support", 
    "Moira": "Support", "Sierra": "Support", "Shion": "Support", "Zenyatta": "Support"
}

# Expanded 53x53 counter dataset matrix
MATRIX_53x53 = {
    "D.Va": {h: 1.0 if h == "Zarya" else (7.5 if h in ["Ashe", "Echo"] else (9.0 if h == "Pharah" else (8.5 if h == "Widowmaker" else 5.0))) for h in HERO_ROLES},
    "D.Mon": {h: 8.5 if h == "Wrecking Ball" else (7.5 if h in ["Genji", "Tracer"] else 5.0) for h in HERO_ROLES},
    "Doomfist": {h: 2.0 if h in ["Roadhog", "Sombra"] else (2.5 if h == "Cassidy" else 5.0) for h in HERO_ROLES},
    "Domina": {h: 5.0 for h in HERO_ROLES},
    "Hazard": {h: 7.5 if h in ["Orisa", "Reinhardt"] else 5.0 for h in HERO_ROLES},
    "Junker Queen": {h: 2.0 if h == "Kiriko" else 5.0 for h in HERO_ROLES},
    "Mauga": {h: 1.5 if h == "Sigma" else (0.5 if h == "Ana" else 5.0) for h in HERO_ROLES},
    "Orisa": {h: 2.5 if h == "Hazard" else (1.5 if h == "Zarya" else 5.0) for h in HERO_ROLES},
    "Ramattra": {h: 5.0 for h in HERO_ROLES},
    "Reinhardt": {h: 2.5 if h == "Hazard" else 5.0 for h in HERO_ROLES},
    "Roadhog": {h: 8.0 if h in ["Doomfist", "Wrecking Ball"] else (8.5 if h == "Winston" else (0.5 if h == "Ana" else (2.5 if h == "Kiriko" else 5.0))) for h in HERO_ROLES},
    "Sigma": {h: 8.5 if h in ["Mauga", "Bastion"] else (7.5 if h in ["Ashe", "Widowmaker"] else (2.5 if h == "Zarya" else 5.0)) for h in HERO_ROLES},
    "Winston": {h: 1.5 if h == "Roadhog" else (8.5 if h == "Genji" else (7.5 if h in ["Hanzo", "Sombra"] else (9.0 if h == "Widowmaker" else (2.5 if h == "Brigitte" else 5.0)))) for h in HERO_ROLES},
    "Wrecking Ball": {h: 1.5 if h == "D.Mon" else (2.0 if h == "Roadhog" else (1.0 if h == "Sombra" else 5.0)) for h in HERO_ROLES},
    "Wuyang": {h: 5.0 for h in HERO_ROLES},
    "Zarya": {h: 9.0 if h == "D.Va" else (8.5 if h == "Orisa" else (7.5 if h == "Sigma" else (8.0 if h == "Genji" else 5.0))) for h in HERO_ROLES},
    "Ashe": {h: 2.5 if h in ["D.Va", "Sigma"] else 5.5 for h in HERO_ROLES},
    "Anran": {h: 5.5 for h in HERO_ROLES},
    "Bastion": {h: 1.5 if h == "Sigma" else (2.5 if h == "Ana" else 5.5) for h in HERO_ROLES},
    "Cassidy": {h: 7.5 if h in ["Doomfist", "Genji", "Pharah", "Sombra"] else (8.5 if h == "Tracer" else 5.5) for h in HERO_ROLES},
    "Echo": {h: 2.5 if h == "D.Va" else 5.5 for h in HERO_ROLES},
    "Emri": {h: 5.5 for h in HERO_ROLES},
    "Enron": {h: 5.5 for h in HERO_ROLES},
    "Genji": {h: 2.5 if h in ["D.Mon", "Cassidy", "Brigitte"] else (1.5 if h == "Winston" else (2.0 if h == "Zarya" else 5.5)) for h in HERO_ROLES},
    "Hanzo": {h: 2.5 if h == "Winston" else 5.5 for h in HERO_ROLES},
    "Junkrat": {h: 1.0 if h == "Pharah" else (2.5 if h == "Juno" else 5.5) for h in HERO_ROLES},
    "Mei": {h: 1.5 if h == "Pharah" else 5.5 for h in HERO_ROLES},
    "Pharah": {h: 1.0 if h == "D.Va" else (2.5 if h == "Cassidy" else (9.0 if h == "Junkrat" else (8.5 if h in ["Mei", "Symmetra"] else 5.5))) for h in HERO_ROLES},
    "Reaper": {h: 2.5 if h == "Juno" else 5.5 for h in HERO_ROLES},
    "Sojourn": {h: 5.5 for h in HERO_ROLES},
    "Soldier: 76": {h: 5.5 for h in HERO_ROLES},
    "Sombra": {h: 8.0 if h in ["Doomfist", "Zenyatta"] else (2.5 if h in ["Winston", "Cassidy", "Brigitte"] else (9.0 if h in ["Wrecking Ball", "Widowmaker"] else 5.5)) for h in HERO_ROLES},
    "Symmetra": {h: 1.5 if h == "Pharah" else 5.5 for h in HERO_ROLES},
    "Torbjörn": {h: 5.5 for h in HERO_ROLES},
    "Tracer": {h: 1.5 if h in ["Cassidy", "Brigitte"] else 5.5 for h in HERO_ROLES},
    "Venture": {h: 5.5 for h in HERO_ROLES},
    "Widowmaker": {h: 1.5 if h == "D.Va" else (2.5 if h == "Sigma" else (1.0 if h in ["Winston", "Sombra"] else 5.5)) for h in HERO_ROLES},
    "Ana": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Orisa", "Ramattra", "Reinhardt", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else (9.5 if h in ["Mauga", "Roadhog"] else (7.5 if h == "Bastion" else (1.5 if h == "Kiriko" else 5.0))) for h in HERO_ROLES},
    "Baptiste": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Brigitte": {h: 7.5 if h in ["Winston", "Genji", "Sombra"] else (8.5 if h == "Tracer" else (4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0)) for h in HERO_ROLES},
    "Freja": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Illari": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Jetpack Cat": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Juno": {h: 7.5 if h in ["Junkrat", "Reaper"] else (4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0) for h in HERO_ROLES},
    "Kiriko": {h: 8.0 if h == "Junker Queen" else (7.5 if h == "Roadhog" else (8.5 if h == "Ana" else (4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0))) for h in HERO_ROLES},
    "Lifeweaver": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Lúcio": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Mercy": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Mizuki": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Moira": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Sierra": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Shion": {h: 4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0 for h in HERO_ROLES},
    "Zenyatta": {h: 2.0 if h == "Sombra" else (4.5 if h in ["D.Va", "D.Mon", "Doomfist", "Domina", "Hazard", "Junker Queen", "Mauga", "Orisa", "Ramattra", "Reinhardt", "Roadhog", "Sigma", "Winston", "Wrecking Ball", "Wuyang", "Zarya"] else 5.0) for h in HERO_ROLES}
}

class OverwatchTruePairwiseEngine:
    def __init__(self):
        self.heroes = HERO_ROLES
        self.matrix = MATRIX_53x53

    def get_matchup_points(self, candidate: str, enemy_team: list) -> float:
        if not enemy_team:
            return 0.0
        total_points = sum(self.matrix[candidate][enemy] for enemy in enemy_team)
        raw_avg = total_points / len(enemy_team)
        return round(raw_avg - 5.0, 2)

    def generate_counter_team(self, enemy_team: list, strictness: float) -> dict:
        candidates = []
        for hero, role in self.heroes.items():
            if hero in enemy_team:
                continue
            score = self.get_matchup_points(hero, enemy_team)
            candidates.append({"name": hero, "role": role, "score": score})

        def pick_for_role(role_name: str, count: int) -> list:
            role_pool = [c for c in candidates if c["role"] == role_name]
            if not role_pool:
                return []
                            # (Inside the pick_for_role helper function)
            role_pool.sort(key=lambda x: x["score"], reverse=True)
            
            # TANK BALANCE OVERRIDE: Shuffle tanks within 0.5 utility value threshold of maximum leader
            if role_name == "Tank" and role_pool:
                top_score = role_pool[0]["score"]
                viable_tanks = [t for t in role_pool if (top_score - t["score"]) <= 0.5]
                random.shuffle(viable_tanks)
                role_pool[:len(viable_tanks)] = viable_tanks
            else:
                random.shuffle(role_pool)
                role_pool.sort(key=lambda x: x["score"], reverse=True)
            
            slice_size = max(count, int(len(role_pool) * (1.1 - strictness)))
            viable_pool = role_pool[:slice_size]
            chosen = random.sample(viable_pool, min(count, len(viable_pool)))
            return [c["name"] for c in chosen]

        return {
            "Tank": pick_for_role("Tank", 1),
            "DPS": pick_for_role("DPS", 2),
            "Support": pick_for_role("Support", 2)
        }


class OverwatchGUI:
    def __init__(self, root):
        self.root = root
        self.root.title("OW Flexible Format Counter Picker")
        self.root.geometry("760x690")
        
        self.BG_MAIN = "#0f0f12"        
        self.BG_PANEL = "#16161a"       
        self.TEXT_LIGHT = "#FFFFFF"     
        self.ACCENT_ORANGE = "#F99E1A"  
        self.ACCENT_RED = "#E63946"     
        self.ACCENT_BLUE = "#4A90E2"    
        self.ACCENT_CYAN = "#00F5D4"    
        self.TEXT_MUTED = "#A0A0A0"     
        
        self.root.configure(bg=self.BG_MAIN)
        self.engine = OverwatchTruePairwiseEngine()
        self.selected_enemies = []
        self._build_ui()

    def _build_ui(self):
        title = tk.Label(self.root, text="OVERWATCH FLEXIBLE DRAFT ENGINE", font=("Arial", 12, "bold"), bg=self.BG_MAIN, fg=self.ACCENT_ORANGE)
        title.pack(pady=10)
        
        format_frame = tk.Frame(self.root, bg=self.BG_MAIN)
        format_frame.pack(fill="x", padx=20, pady=(0, 10))
        tk.Label(format_frame, text="Active Match Format:", bg=self.BG_MAIN, fg=self.TEXT_LIGHT, font=("Arial", 10, "bold")).pack(side="left", padx=(0, 10))
        
        self.format_var = tk.StringVar(value="5v5")
        rb_5v5 = tk.Radiobutton(format_frame, text="5v5 Format", variable=self.format_var, value="5v5", bg=self.BG_MAIN, fg=self.TEXT_LIGHT, selectcolor=self.BG_PANEL, activebackground=self.BG_MAIN, activeforeground=self.ACCENT_ORANGE, command=self.clear_all)
        rb_5v5.pack(side="left", padx=10)
        rb_6v6 = tk.Radiobutton(format_frame, text="6v6 Playtest", variable=self.format_var, value="6v6", bg=self.BG_MAIN, fg=self.TEXT_LIGHT, selectcolor=self.BG_PANEL, activebackground=self.BG_MAIN, activeforeground=self.ACCENT_ORANGE, command=self.clear_all)
        rb_6v6.pack(side="left", padx=10)
        
        deck_frame = tk.Frame(self.root, bg=self.BG_MAIN)
        deck_frame.pack(fill="both", expand=True, padx=20)
        
        pool_frame = tk.Frame(deck_frame, bg=self.BG_PANEL, bd=2, relief="groove")
        pool_frame.pack(side="left", fill="both", expand=True, padx=(0, 10))
        tk.Label(pool_frame, text="Available Roster", font=("Arial", 11, "bold"), bg=self.BG_PANEL, fg=self.ACCENT_ORANGE).pack(pady=5)
        
        # SEARCH BAR LAYER
        search_frame = tk.Frame(pool_frame, bg=self.BG_PANEL)
        search_frame.pack(fill="x", padx=5, pady=(0, 5))
        tk.Label(search_frame, text=" Search:", bg=self.BG_PANEL, fg=self.TEXT_LIGHT, font=("Arial", 9, "bold")).pack(side="left", padx=(0, 5))
        self.search_var = tk.StringVar()
        self.search_var.trace_add("write", lambda *args: self.filter_roster())
        search_entry = tk.Entry(search_frame, textvariable=self.search_var, bg=self.BG_MAIN, fg=self.TEXT_LIGHT, insertbackground=self.TEXT_LIGHT, bd=1, relief="solid")
        search_entry.pack(side="left", fill="x", expand=True)

        self.hero_listbox = tk.Listbox(pool_frame, bg=self.BG_MAIN, fg=self.TEXT_LIGHT, selectbackground=self.ACCENT_ORANGE, font=("Arial", 10), bd=0)
        self.hero_listbox.pack(fill="both", expand=True, padx=5, pady=5)
        
        add_btn = tk.Button(pool_frame, text="Add to Enemy Team ➔", font=("Arial", 10, "bold"), bg=self.ACCENT_ORANGE, fg=self.TEXT_LIGHT, command=self.add_hero, activebackground="#D16617")
        add_btn.pack(fill="x", padx=5, pady=5)
        
        enemy_frame = tk.Frame(deck_frame, bg=self.BG_PANEL, bd=2, relief="groove", width=250)
        enemy_frame.pack(side="right", fill="both", expand=False, padx=(10, 0))
        enemy_frame.pack_propagate(False)
        tk.Label(enemy_frame, text="Enemy Lineup Monitor", font=("Arial", 8, "bold"), bg=self.BG_PANEL, fg=self.ACCENT_RED).pack(pady=5)
        
        self.enemy_listbox = tk.Listbox(enemy_frame, bg=self.BG_MAIN, fg=self.TEXT_LIGHT, font=("Arial", 10), bd=0)
        self.enemy_listbox.pack(fill="both", expand=True, padx=5, pady=5)
        
        remove_btn = tk.Button(enemy_frame, text="Remove Selected", bg=self.ACCENT_RED, fg=self.TEXT_LIGHT, command=self.remove_hero)
        remove_btn.pack(fill="x", padx=5, pady=5)
        
        control_frame = tk.Frame(self.root, bg=self.BG_MAIN)
        control_frame.pack(fill="x", padx=20, pady=15)
        tk.Label(control_frame, text="Draft Pool Strictness:", bg=self.BG_MAIN, fg=self.TEXT_LIGHT).pack(side="left", padx=(0, 10))
        
        self.strict_slider = tk.Scale(control_frame, from_=0.0, to=1.0, resolution=0.1, orient="horizontal", bg=self.BG_MAIN, fg=self.TEXT_LIGHT, highlightthickness=0)
        self.strict_slider.set(0.8)
        self.strict_slider.pack(side="left", fill="x", expand=True, padx=(0, 20))
        
        calc_btn = tk.Button(control_frame, text="Calculate Counter Team", font=("Arial", 10, "bold"), bg=self.ACCENT_BLUE, fg=self.TEXT_LIGHT, command=self.calculate_counter)
        calc_btn.pack(side="right", ipadx=10, ipady=5)
        
        output_frame = tk.Frame(self.root, bg=self.BG_PANEL, bd=2, relief="sunken")
        output_frame.pack(fill="x", padx=20, pady=(0, 20), ipady=5)
        tk.Label(output_frame, text="OPTIMAL COMPOSITION VIA MEAN SCALE UTILITY", font=("Arial", 11, "bold"), bg=self.BG_PANEL, fg=self.ACCENT_BLUE).pack(pady=5)
        
        self.result_vars = {
            "Tank": tk.StringVar(value="-"),
            "DPS": tk.StringVar(value="-"),
            "Support": tk.StringVar(value="-")
        }
        for role, var in self.result_vars.items():
            r_frame = tk.Frame(output_frame, bg=self.BG_PANEL)
            r_frame.pack(fill="x", padx=30, pady=2)
            tk.Label(r_frame, text=f"{role.ljust(8)}:", font=("Courier", 11, "bold"), bg=self.BG_PANEL, fg=self.TEXT_MUTED).pack(side="left")
            tk.Label(r_frame, textvariable=var, font=("Arial", 11, "bold"), bg=self.BG_PANEL, fg=self.ACCENT_CYAN).pack(side="left", padx=10)
        
        self.filter_roster()

    def filter_roster(self):
        search_term = self.search_var.get().lower()
        self.hero_listbox.delete(0, tk.END)
        sorted_heroes = sorted(list(HERO_ROLES.keys()))
        for h in sorted_heroes:
            if search_term in h.lower():
                self.hero_listbox.insert(tk.END, f"{h} ({HERO_ROLES[h]})")

    def add_hero(self):
        selection = self.hero_listbox.curselection()
        if not selection:
            return
        hero_string = self.hero_listbox.get(selection[0])
        hero_name = hero_string.split(" (")[0]
        max_size = 5 if self.format_var.get() == "5v5" else 6
        
        if len(self.selected_enemies) >= max_size:
            messagebox.showwarning("Team Full", f"The chosen format holds a maximum of {max_size} heroes.")
            return
        if hero_name in self.selected_enemies:
            messagebox.showwarning("Duplicate", "This hero is already in the enemy line-up.")
            return
            
        self.selected_enemies.append(hero_name)
        self.enemy_listbox.insert(tk.END, f"• {hero_name}")

    def remove_hero(self):
        selection = self.enemy_listbox.curselection()
        if not selection:
            return
        idx = selection[0]
        self.enemy_listbox.delete(idx)
        del self.selected_enemies[idx]

    def clear_all(self):
        self.selected_enemies.clear()
        self.enemy_listbox.delete(0, tk.END)
        self.search_var.set("")
        for var in self.result_vars.values():
            var.set("-")

    def calculate_counter(self):
        if not self.selected_enemies:
            messagebox.showinfo("Empty", "Add at least one enemy hero first!")
            return
            
        strictness_val = self.strict_slider.get()
        comp = self.engine.generate_counter_team(self.selected_enemies, strictness=strictness_val)
        self.result_vars["Tank"].set(", ".join(comp["Tank"]))
        self.result_vars["DPS"].set(", ".join(comp["DPS"]))
        self.result_vars["Support"].set(", ".join(comp["Support"]))


if __name__ == "__main__":
    root = tk.Tk()
    app = OverwatchGUI(root)
    root.mainloop()
