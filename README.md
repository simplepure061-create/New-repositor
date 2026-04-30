-- [[ 👑 NinjaPremium Pro 終極修復優化版 ]] --
local P = game:GetService("Players").LocalPlayer
local R = game:GetService("RunService")
local U = game:GetService("UserInputService")
local Mouse = P:GetMouse()
local Key_File = "Ninjai_Cache.txt"

local Config = {
    Settings = {Fling = false, Noclip = false, InfJump = false, AntiFall = false, Aimbot = false},
    AimbotRange = 500
}

-- 核心：獲取最近玩家
local function GetClosestPlayer()
    local closest, dist = nil, Config.AimbotRange
    for _, v in pairs(game:GetService("Players"):GetPlayers()) do
        if v ~= P and v.Character and v.Character:FindFirstChild("HumanoidRootPart") and v.Character:FindFirstChild("Humanoid").Health > 0 then
            local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(v.Character.HumanoidRootPart.Position)
            if onScreen then
                local mag = (Vector2.new(pos.X, pos.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
                if mag < dist then
                    dist = mag
                    closest = v
                end
            end
        end
    end
    return closest
end

-- 主 UI 載入
local function LoadUI()
    local g = Instance.new("ScreenGui", P.PlayerGui)
    g.Name = "NinjaUI"; g.ResetOnSpawn = false
    
    local M = Instance.new("Frame", g)
    M.Size = UDim2.new(0, 400, 0, 320); M.Position = UDim2.new(0.5, -200, 0.5, -160)
    M.BackgroundColor3 = Color3.fromRGB(20, 20, 20); M.Active = true; M.Draggable = true
    Instance.new("UICorner", M)

    local ToggleBtn = Instance.new("TextButton", g)
    ToggleBtn.Size = UDim2.new(0, 80, 0, 30); ToggleBtn.Position = UDim2.new(0, 10, 0, 10)
    ToggleBtn.Text = "選單開關"; ToggleBtn.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
    ToggleBtn.TextColor3 = Color3.fromRGB(255,255,255); Instance.new("UICorner", ToggleBtn)
    
    local function ToggleMenu() M.Visible = not M.Visible end
    ToggleBtn.MouseButton1Click:Connect(ToggleMenu)
    U.InputBegan:Connect(function(i, p) if not p and i.KeyCode == Enum.KeyCode.Insert then ToggleMenu() end end)

    local T = Instance.new("TextLabel", M)
    T.Text = "NINJA PRO [Insert 鍵控制]"; T.Size = UDim2.new(1, 0, 0, 40); T.TextColor3 = Color3.fromRGB(255, 50, 50); T.BackgroundTransparency = 1

    local C = Instance.new("ScrollingFrame", M)
    C.Size = UDim2.new(1, -20, 1, -60); C.Position = UDim2.new(0, 10, 0, 50)
    C.BackgroundTransparency = 1; Instance.new("UIListLayout", C).Padding = UDim.new(0, 5)

    local function NewBtn(txt, k)
        local b = Instance.new("TextButton", C)
        b.Size = UDim2.new(1, 0, 0, 40); b.Text = "  " .. txt .. " [ 關閉 ]"
        b.BackgroundColor3 = Color3.fromRGB(40, 40, 40); b.TextColor3 = Color3.fromRGB(200, 200, 200)
        b.TextXAlignment = Enum.TextXAlignment.Left; Instance.new("UICorner", b)

        b.MouseButton1Click:Connect(function()
            Config.Settings[k] = not Config.Settings[k]
            b.Text = "  " .. txt .. (Config.Settings[k] and " [ 開啟 ]" or " [ 關閉 ]")
            b.BackgroundColor3 = Config.Settings[k] and Color3.fromRGB(255, 50, 50) or Color3.fromRGB(40, 40, 40)
        end)
    end

    NewBtn("強力甩飛 (Fling)", "Fling")
    NewBtn("穿牆模式 (Noclip)", "Noclip")
    NewBtn("無限跳躍 (InfJump)", "InfJump")
    NewBtn("跌落回傳 (AntiFall)", "AntiFall")
    NewBtn("全自動鎖頭 (Aimbot)", "Aimbot")

    -- 核心循環：確保所有功能在每一幀生效
    R.RenderStepped:Connect(function()
        pcall(function()
            local Char = P.Character
            if not Char or not Char:FindFirstChild("HumanoidRootPart") then return end

            -- 1. Fling
            if Config.Settings.Fling then 
                Char.HumanoidRootPart.RotVelocity = Vector3.new(0, 9e8, 0) 
                Char.HumanoidRootPart.Velocity = Vector3.new(0, 0, 0) -- 保持原地旋轉
            end
            
            -- 2. Noclip
            if Config.Settings.Noclip then 
                for _,v in pairs(Char:GetDescendants()) do 
                    if v:IsA("BasePart") then v.CanCollide = false end 
                end 
            end
            
            -- 3. AntiFall
            if Config.Settings.AntiFall and Char.HumanoidRootPart.Position.Y < -500 then 
                Char.HumanoidRootPart.CFrame = CFrame.new(0, 100, 0) 
            end
            
            -- 4. Aimbot (只要開啟就自動鎖定，無需右鍵)
            if Config.Settings.Aimbot then
                local target = GetClosestPlayer()
                if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                    workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, target.Character.HumanoidRootPart.Position)
                end
            end
        end)
    end)

    U.JumpRequest:Connect(function() 
        if Config.Settings.InfJump and P.Character and P.Character:FindFirstChildOfClass("Humanoid") then 
            P.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping") 
        end 
    end)
end

-- 驗證邏輯
local Current_Key = "NINJA-" .. math.random(1000, 9999) -- 固定一下格式
local function Auth()
    local g = Instance.new("ScreenGui", P.PlayerGui); g.DisplayOrder = 999
    local f = Instance.new("Frame", g); f.Size = UDim2.new(0, 300, 0, 200); f.Position = UDim2.new(0.5, -150, 0.4, -100); f.BackgroundColor3 = Color3.fromRGB(30, 30, 30); Instance.new("UICorner", f)
    local b = Instance.new("TextButton", f); b.Text = "獲取驗證碼 (點擊)"; b.Size = UDim2.new(0.8, 0, 0, 40); b.Position = UDim2.new(0.1, 0, 0.2, 0); Instance.new("UICorner", b)
    local i = Instance.new("TextBox", f); i.PlaceholderText = "輸入 Key"; i.Size = UDim2.new(0.8, 0, 0, 40); i.Position = UDim2.new(0.1, 0, 0.5, 0); Instance.new("UICorner", i)
    local l = Instance.new("TextButton", f); l.Text = "確認驗證"; l.Size = UDim2.new(0.8, 0, 0, 40); l.Position = UDim2.new(0.1, 0, 0.8, 0); l.BackgroundColor3 = Color3.fromRGB(255, 50, 50); Instance.new("UICorner", l)

    b.MouseButton1Click:Connect(function() b.Text = Current_Key end)
    l.MouseButton1Click:Connect(function()
        if i.Text == Current_Key then
            if writefile then pcall(function() writefile(Key_File, "OK") end) end
            g:Destroy(); LoadUI()
        else
            i.Text = ""; i.PlaceholderText = "錯誤！請重新輸入"
        end
    end)
end

if isfile and isfile(Key_File) then LoadUI() else Auth() end


loadstring(game:HttpGet("https://githubusercontent.com"))()



# New-repositor
