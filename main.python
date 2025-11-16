import os
import json
import re
import time
from datetime import datetime
from pathlib import Path

VERSION = "v1.0"

class MinecraftProfile:
    def __init__(self, id, name):
        self.id = id
        self.name = name
    
    def to_dict(self):
        return {"id": self.id, "name": self.name}
    
    @classmethod
    def from_dict(cls, data):
        return cls(data["id"], data["name"])

class Account:
    def __init__(self, access_token, access_token_expires_at, eligible_for_migration, 
                 has_multiple_profiles, legacy, persistent, user_properties, 
                 local_id, minecraft_profile, remote_id, type_, username):
        self.access_token = access_token
        self.access_token_expires_at = access_token_expires_at
        self.eligible_for_migration = eligible_for_migration
        self.has_multiple_profiles = has_multiple_profiles
        self.legacy = legacy
        self.persistent = persistent
        self.user_properties = user_properties
        self.local_id = local_id
        self.minecraft_profile = minecraft_profile
        self.remote_id = remote_id
        self.type = type_
        self.username = username
    
    def to_dict(self):
        return {
            "accessToken": self.access_token,
            "accessTokenExpiresAt": self.access_token_expires_at,
            "eligibleForMigration": self.eligible_for_migration,
            "hasMultipleProfiles": self.has_multiple_profiles,
            "legacy": self.legacy,
            "persistent": self.persistent,
            "userProperites": self.user_properties,
            "localId": self.local_id,
            "minecraftProfile": self.minecraft_profile.to_dict(),
            "remoteId": self.remote_id,
            "type": self.type,
            "username": self.username
        }
    
    @classmethod
    def from_dict(cls, data):
        return cls(
            access_token=data["accessToken"],
            access_token_expires_at=data["accessTokenExpiresAt"],
            eligible_for_migration=data["eligibleForMigration"],
            has_multiple_profiles=data["hasMultipleProfiles"],
            legacy=data["legacy"],
            persistent=data["persistent"],
            user_properties=data["userProperites"],
            local_id=data["localId"],
            minecraft_profile=MinecraftProfile.from_dict(data["minecraftProfile"]),
            remote_id=data["remoteId"],
            type_=data["type"],
            username=data["username"]
        )

class AccountsData:
    def __init__(self, accounts=None):
        self.accounts = accounts or {}
    
    def to_dict(self):
        return {"accounts": {k: v.to_dict() for k, v in self.accounts.items()}}
    
    @classmethod
    def from_dict(cls, data):
        accounts = {}
        for uuid, account_data in data.get("accounts", {}).items():
            accounts[uuid] = Account.from_dict(account_data)
        return cls(accounts)

class CrackedLunarAccountTool:
    def __init__(self):
        home_dir = Path.home()
        self.lunar_accounts_path = home_dir / ".lunarclient" / "settings" / "game" / "accounts.json"
        self.accounts_data = None
    
    def load_json(self):
        if not self.lunar_accounts_path.exists():
            self.accounts_data = AccountsData()
            return True
        
        try:
            with open(self.lunar_accounts_path, 'r', encoding='utf-8') as file:
                data = json.load(file)
                self.accounts_data = AccountsData.from_dict(data)
            return True
        except Exception as e:
            self.print_line("ERROR", f"Failed to load accounts file: {e}", "\033[31m")
            self.print_line("NOTICE", "Please check that you have Lunar Client installed.", "\033[31m")
            return False
    
    def save_json(self):
        try:
            self.lunar_accounts_path.parent.mkdir(parents=True, exist_ok=True)
            with open(self.lunar_accounts_path, 'w', encoding='utf-8') as file:
                json.dump(self.accounts_data.to_dict(), file, indent=2)
            return True
        except Exception as e:
            self.print_line("ERROR", f"Failed to save accounts file: {e}", "\033[31m")
            return False
    
    def create_account(self, username, uuid):
        new_account = Account(
            access_token=uuid,
            access_token_expires_at="2050-07-02T10:56:30.717167800Z",
            eligible_for_migration=False,
            has_multiple_profiles=False,
            legacy=True,
            persistent=True,
            user_properties=[],
            local_id=uuid,
            minecraft_profile=MinecraftProfile(id=uuid, name=username),
            remote_id=uuid,
            type_="Xbox",
            username=username
        )
        
        self.accounts_data.accounts[uuid] = new_account
        self.print_line("SUCCESS", "Your account has successfully been created.", "\033[38;5;39m")
    
    def remove_all_accounts(self):
        self.accounts_data.accounts = {}
        self.print_line("SUCCESS", "All accounts have been successfully removed.", "\033[38;5;39m")
    
    def remove_cracked_accounts(self):
        keys_to_remove = []
        for uuid, account in self.accounts_data.accounts.items():
            if self.is_valid_uuid(account.access_token):
                keys_to_remove.append(uuid)
        
        for key in keys_to_remove:
            del self.accounts_data.accounts[key]
        
        self.print_line("SUCCESS", "Cracked accounts have been successfully removed.", "\033[38;5;39m")
    
    def remove_premium_accounts(self):
        keys_to_remove = []
        for uuid, account in self.accounts_data.accounts.items():
            if not self.is_valid_uuid(account.access_token):
                keys_to_remove.append(uuid)
        
        for key in keys_to_remove:
            del self.accounts_data.accounts[key]
        
        self.print_line("SUCCESS", "Premium accounts have been successfully removed.", "\033[38;5;39m")
    
    def view_installed_accounts(self):
        self.print_line("INFO", "Installed Accounts:", "\033[38;5;39m")
        for uuid, account in self.accounts_data.accounts.items():
            self.print_line("ACCOUNT", f"{uuid}: {account.username}", "\033[38;5;39m")
    
    def is_valid_minecraft_username(self, username):
        if len(username) < 3 or len(username) > 16:
            return False
        return bool(re.match(r'^[a-zA-Z0-9_]+$', username))
    
    def is_valid_uuid(self, uuid):
        return bool(re.match(r'^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12}$', uuid))
    
    def print(self, info, text, color):
        timestamp = datetime.now().strftime("%H:%M:%S")
        print(f" [{timestamp}] > [{info}] {color}{text}\033[0m", end="")
    
    def print_line(self, info, text, color):
        timestamp = datetime.now().strftime("%H:%M:%S")
        print(f" [{timestamp}] > [{info}] {color}{text}\033[0m")
    
    def clear_screen(self):
        os.system('cls' if os.name == 'nt' else 'clear')
    
    def print_menu(self):
        self.print_line("?", "What would you like to do:", "\033[38;5;39m")
        self.print_line("OPTION", "1. Create Account", "\033[38;5;39m")
        self.print_line("OPTION", "2. Remove Accounts", "\033[38;5;39m")
        self.print_line("OPTION", "3. View Installed Accounts", "\033[38;5;39m")
        self.print_line("OPTION", "4. Exit the program", "\033[38;5;39m")
    
    def remove_accounts_menu(self):
        self.clear_screen()
        self.print_line("?", "Choose an option to remove accounts:", "\033[38;5;39m")
        self.print_line("OPTION", "1. Remove All Accounts", "\033[38;5;39m")
        self.print_line("OPTION", "2. Remove Cracked Accounts (accessToken is not a UUID)", "\033[38;5;39m")
        self.print_line("OPTION", "3. Remove Premium Accounts (accessToken is a UUID)", "\033[38;5;39m")
        
        choice = input("Please type your option (1-3) here: ").strip()
        
        if choice == "1":
            self.remove_all_accounts()
        elif choice == "2":
            self.remove_cracked_accounts()
        elif choice == "3":
            self.remove_premium_accounts()
        else:
            self.print_line("ERROR", "Invalid option. Returning to main menu.", "\033[31m")
        
        self.save_json()
    
    def create_account_prompt(self):
        username = input("Enter your desired username: ").strip()
        
        if not self.is_valid_minecraft_username(username):
            self.print_line("WARNING", "You may experience issues joining servers because of your username being invalid.", "\033[31m")
        
        while True:
            uuid = input("Enter a valid UUID: ").strip()
            
            if not self.is_valid_uuid(uuid):
                self.print_line("WARNING", "The UUID you entered is invalid. Please ensure it follows the correct format.", "\033[31m")
                retry = input("Would you like to try again? (y/n): ").strip().lower()
                if retry == "n":
                    self.print_line("INFO", "Returning to main menu.", "\033[38;5;39m")
                    return
            else:
                self.create_account(username, uuid)
                self.save_json()
                break
    
    def run(self):
        if not self.load_json():
            self.print_line("NOTICE", "Exiting in 3 seconds...", "\033[33m")
            time.sleep(3)
            return
        
        continue_program = True
        
        while continue_program:
            self.clear_screen()
            print(f"Cracked Lunar Account Tool (Python) {VERSION}\n")
            
            self.print_menu()
            
            choice = input("Please type your option (1-4) here: ").strip()
            
            if choice == "1":
                self.create_account_prompt()
            elif choice == "2":
                self.remove_accounts_menu()
            elif choice == "3":
                self.view_installed_accounts()
            elif choice == "4":
                self.print_line("INFO", "Exiting the program.", "\033[38;5;39m")
                continue_program = False
            else:
                self.print_line("ERROR", "Your choice is invalid. Please pick an option (1-4).", "\033[31m")
            
            if continue_program:
                self.print_line("INFO", "Press any key to return to the main menu...", "\033[38;5;39m")
                input()

def main():    
    tool = CrackedLunarAccountTool()
    tool.run()

if __name__ == "__main__":
    main()
