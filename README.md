-- [[ 👑 NinjaPremium Pro 核心主程式 ]] --
local P = game:GetService("Players").LocalPlayer
local R = game:GetService("RunService")
local U = game:GetService("UserInputService")
local Key_File = "NinjaAuth_Cache.txt"
local Config = {Settings = {Fling = false, Noclip = false, InfJump = false, AntiFall = false}}

-- 隨機 Key 生成
local function GetKey()
    local c = "ABCDEF123456"
    local r = ""
    for i=1,6 do local s = math.random(1,#c) r = r..c:sub(s,s) end
    return r
end
local Current_Key = GetKey()

-- 主介面 (重生不消失 + Insert 鍵開關)
local function LoadUI()
    local g = Instance.new("ScreenGui", P.PlayerGui); g.Name = "NinjaUI"; g.ResetOnSpawn = false
    local M = Instance.new("Frame", g); M.Size = UDim2.new(0, 400, 0, 280); M.Position = UDim2.new(0.5, -200, 0.5, -140)
    M.BackgroundColor3 = Color3.fromRGB(20, 20, 20); M.Active = true; M.Draggable = true; Instance.new("UICorner", M)

    U.InputBegan:Connect(function(i, p) if not p and i.KeyCode == Enum.KeyCode.Insert then M.Visible = not M.Visible end end)

    local T = Instance.new("TextLabel", M); T.Text = "NINJA PREMIUM PRO [Insert 開關]"; T.Size = UDim2.new(1, 0, 0, 40)
    T.TextColor3 = Color3.fromRGB(255, 50, 50); T.BackgroundTransparency = 1

    local C = Instance.new("ScrollingFrame", M); C.Size = UDim2.new(1, -20, 1, -50); C.Position = UDim2.new(0, 10, 0, 45)
    C.BackgroundTransparency = 1; Instance.new("UIListLayout", C).Padding = UDim.new(0, 5)

    local function NewBtn(txt, k, cb)
        local b = Instance.new("TextButton", C); b.Size = UDim2.new(1, 0, 0, 35); b.Text = txt; b.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        b.TextColor3 = Color3.fromRGB(200, 200, 200); Instance.new("UICorner", b)
        b.MouseButton1Click:Connect(function()
            Config.Settings[k] = not Config.Settings[k]
            b.BackgroundColor3 = Config.Settings[k] and Color3.fromRGB(255, 50, 50) or Color3.fromRGB(40, 40, 40)
            if cb then cb() end
        end)
    end

    NewBtn("強力甩飛 (Fling)", "Fling")
    NewBtn("穿牆模式 (Noclip)", "Noclip")
    NewBtn("無限跳躍 (InfJump)", "InfJump")
    NewBtn("掉落回傳 (AntiFall)", "AntiFall")
    NewBtn("💡 接入外部自瞄", "Aim", function() loadstring(game:HttpGet("https://githubusercontent.com"))() end)

    R.Heartbeat:Connect(function()
        pcall(function()
            if Config.Settings.Fling then P.Character.HumanoidRootPart.RotVelocity = Vector3.new(0, 9e8, 0) end
            if Config.Settings.Noclip then for _,v in pairs(P.Character:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end end
            if Config.Settings.AntiFall and P.Character.HumanoidRootPart.Position.Y < -500 then P.Character.HumanoidRootPart.CFrame = CFrame.new(0, 100, 0) end
        end)
    end)
    U.JumpRequest:Connect(function() if Config.Settings.InfJump then P.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping") end end)
end

-- 驗證介面 (機器人勾選)
local function Auth()
    local g = Instance.new("ScreenGui", P.PlayerGui); local f = Instance.new("Frame", g)
    f.Size = UDim2.new(0, 250, 0, 150); f.Position = UDim2.new(0.5, -125, 0.4, -75); f.BackgroundColor3 = Color3.fromRGB(30, 30, 30); Instance.new("UICorner", f)
    local b = Instance.new("TextButton", f); b.Text = "我不是機器人 (獲取 Key)"; b.Size = UDim2.new(0.8, 0, 0, 40); b.Position = UDim2.new(0.1, 0, 0.2, 0)
    local i = Instance.new("TextBox", f); i.PlaceholderText = "輸入 Key"; i.Size = UDim2.new(0.8, 0, 0, 35); i.Position = UDim2.new(0.1, 0, 0.6, 0); i.Visible = false
    b.MouseButton1Click:Connect(function() b.Text = "Key: "..Current_Key; i.Visible = true end)
    i:GetPropertyChangedSignal("Text"):Connect(function() if i.Text == Current_Key then if writefile then writefile(Key_File, "OK") end g:Destroy(); LoadUI() end end)
end

if isfile and isfile(Key_File) then LoadUI() else Auth() end



# New-repositor
