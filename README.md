local Rayfield = loadstring(game:HttpGet("https://raw.githubusercontent.com/shlexware/Rayfield/main/source"))()

local Window = Rayfield:CreateWindow({
   Name = "Chatnox Hub | Blox Fruits",
   LoadingTitle = "Chatnox Hub",
   LoadingSubtitle = "Loading...",
   ConfigurationSaving = {
      Enabled = true,
      FolderName = nil,
      FileName = "ChatnoxConfig"
   },
   Discord = { Enabled = false },
   KeySystem = false
})

local AutoFarmTab = Window:CreateTab("Auto Farm", nil)
AutoFarmTab:CreateToggle({
   Name = "Ativar Auto Farm",
   CurrentValue = false,
   Callback = function(Value)
      getgenv().AutoFarm = Value
      while AutoFarm and task.wait() do
         pcall(function()
            local player = game.Players.LocalPlayer
            local char = player.Character or player.CharacterAdded:Wait()
            local humanoid = char:FindFirstChildOfClass("Humanoid")
            local enemies = workspace.Enemies:GetChildren()
            for _, enemy in pairs(enemies) do
               if enemy:FindFirstChild("HumanoidRootPart") and enemy:FindFirstChildOfClass("Humanoid") then
                  repeat
                     task.wait()
                     char.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0, 10, 0)
                     game:GetService("VirtualInputManager"):SendKeyEvent(true, "E", false, game)
                     humanoid:ChangeState(3)
                  until not AutoFarm or enemy.Humanoid.Health <= 0
               end
            end
         end)
      end
   end
})

local FruitTab = Window:CreateTab("Frutas", nil)
FruitTab:CreateToggle({
   Name = "Sniper de Frutas",
   CurrentValue = false,
   Callback = function(Value)
      getgenv().FruitSniper = Value
      local frutasDesejadas = {
         "Dragon-Dragon", "Dough-Dough", "Leopard-Leopard", "Mammoth-Mammoth"
      }
      while FruitSniper and task.wait(60) do
         local frutas = require(game.ReplicatedStorage:WaitForChild("Remotes").CommF_):InvokeServer("GetFruits")
         for _, fruta in pairs(frutas) do
            if table.find(frutasDesejadas, fruta.Name) and fruta.Stock > 0 then
               require(game.ReplicatedStorage.Remotes.CommF_):InvokeServer("BuyFruit", fruta.Name)
               Rayfield:Notify({
                  Title = "Chatnox",
                  Content = "Comprada: " .. fruta.Name,
                  Duration = 6
               })
            end
         end
      end
   end
})

local ServerTab = Window:CreateTab("Server Hop", nil)
ServerTab:CreateButton({
   Name = "Trocar de Servidor",
   Callback = function()
      local Http = game:GetService("HttpService")
      local TP = game:GetService("TeleportService")
      local Servers = Http:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/2753915549/servers/Public?sortOrder=Asc&limit=100"))
      for _, v in pairs(Servers.data) do
         if v.playing < v.maxPlayers then
            TP:TeleportToPlaceInstance(game.PlaceId, v.id)
