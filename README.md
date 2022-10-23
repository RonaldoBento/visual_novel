# visual_novel
 
## Visual Novel desenvolvido na engine RPG Maker VX Ace

<br><img src="./img/logo-bento-projeto.png" alt="logo bento-projeto no formato png"><br>

## Ficha Técnica

<ul>
    <li><strong>Criador:</strong> Ronaldo Bento</li>
    <li><strong>Gênero:</strong> Visual Novel/Terror</li>
    <li><strong>Engine:</strong> RPG Maker VX Ace</li>
    <li><strong>Design e Characters & Scenario: </strong>Horang </li>
    <li><strong>Roteiro original:</strong> Horang </li>
     <li><strong>Roteiro alterado:</strong> Ronaldo Bento</li>
    <li><strong>Idioma:</strong> Português Brasileiro</li>
    <li><strong>Faixa Etária:</strong> Maiores de 18 anos</li>
    <li><storng>Precisa de RTP?</strong> Não</li>
    <li><strong>Início do projeto:</strong> 18/10/2022</li>
    <li><strong>Lançamento do projeto:</strong> 23/10/2022</li>
    <li><strong>Tamanho:</strong> 25MB</li>
    <li><strong>Duração do jogo:</strong> aproximadamente 10 minutos</li>
</ul>

<p>O RPG Maker permite que os usuários criem seus próprios jogos de RPG e com algumas mudanças no sistema pode criar até outros tipos de jogos. Utiliza a linguagem de Programacão:
RGSS (Ruby Game Scripting System) uses the object-oriented scripting language Ruby to develop 2D games for the Windows® platform.</p>

## Exemplos de Scripts:

```Ruby
if true
#==============================================================================
# +++ MOG - Title Multilogo - [Stand-Alone Series] (V1.1) +++
#==============================================================================
# By Moghunter 
# https://atelierrgss.wordpress.com/
#==============================================================================
# Adiciona logos antes da tela de título
#==============================================================================
# Para adicionar os logos nomeie os arquivos da seguinte forma.
#
# Logo_0.png
# Logo_1.png
# Logo_2.png
# Logo_3.png
# Logo_4.png
# Logo_5.png ... Logo_999.png
#
# Grave as imagens na pasta /Graphics/Title1/
#==============================================================================
module MOG_MLOGOS
  #--------------------------------------------------------------------------
  # Definição do nome do arquivo.
  #--------------------------------------------------------------------------
  FILE_NAME = "Logo_"
  #--------------------------------------------------------------------------
  #Ativar ME (coloque " PLAY_ME = nil " para desativar o efeito )
  #--------------------------------------------------------------------------
  PLAY_ME = "Shock"  
  #--------------------------------------------------------------------------
  #Duração do logo.
  #--------------------------------------------------------------------------
  LOGO_DURATION = 80
  #--------------------------------------------------------------------------
  #Ativar o Botão para pular o logo.
  #--------------------------------------------------------------------------
  SKIP_BUTTON = true
end

$imported = {} if $imported.nil?
$imported[:mog_title_multilogo] = true

#==============================================================================
# ■ Scene Tittle
#==============================================================================
class Scene_Title < Scene_Base
  
 #--------------------------------------------------------------------------
 # ● Main
 #--------------------------------------------------------------------------             
   alias mog_t_logo_main main
   def main
       execute_tlogo if $logo_enabled == nil
       mog_t_logo_main
   end

 #--------------------------------------------------------------------------
 # ● Execute Tlogo
 #--------------------------------------------------------------------------             
   def execute_tlogo
       $logo_enabled = true if $logo_enabled == nil
       Graphics.transition(transition_speed); mlogo = M_logo.new
       loop do
       Input.update ; mlogo.update ; Graphics.update ; break if mlogo.phase == 1
       end
   end
   
end
 
#==============================================================================
# ■ M_Logo
#==============================================================================
class M_logo
  include MOG_MLOGOS
  attr_accessor :phase
  
 #--------------------------------------------------------------------------
 # ● Initialize
 #--------------------------------------------------------------------------             
  def initialize
      @phase = 0 ; @logos = []
      for i in 0...999 ; @logos.push(Cache.title1(MOG_MLOGOS::FILE_NAME + i.to_s)) rescue nil      
      break if @logos[i] == nil ; end
      @logo_sprite = Sprite.new ; time = [LOGO_DURATION, 5].max
      @logo_data = [0,0,61 + time] ; refresh_logo
      Audio.me_play("Audio/ME/" + PLAY_ME, 100, 100) if PLAY_ME != nil
  end
  
 #--------------------------------------------------------------------------
 # ● Refresh Logo
 #--------------------------------------------------------------------------             
  def refresh_logo
      if @logos[@logo_data[1]] == nil ; @phase = 1 ; return ; end
      @logo_sprite.bitmap = @logos[@logo_data[1]]
      @logo_sprite.opacity = 0 ; @logo_data[0] = 0 ; @logo_data[1] += 1
  end
  
 #--------------------------------------------------------------------------
 # ● Dispose
 #--------------------------------------------------------------------------             
  def dispose
      Graphics.freeze
      @logos.each {|sprite| sprite.dispose } 
      @logo_sprite.bitmap.dispose ; @logo_sprite.dispose
  end
  
 #--------------------------------------------------------------------------
 # ● Update
 #--------------------------------------------------------------------------             
  def update
      update_skip_logo ; @logo_data[0] += 1
      case @logo_data[0]
      when 0..60 ; @logo_sprite.opacity += 5
      when 61..@logo_data[2] ; @logo_sprite.opacity = 255
      when @logo_data[2]..(@logo_data[2] + 120) ; @logo_sprite.opacity -= 5
      end      
      refresh_logo if @logo_sprite.opacity == 0
  end
  
 #--------------------------------------------------------------------------
 # ● Update Skip Logo
 #--------------------------------------------------------------------------             
  def update_skip_logo
      return if !SKIP_BUTTON
      return if @logo_data[1] > @logos.size
      if Input.press?(:C) or Input.press?(:B) 
      @logo_data[0] = @logo_data[2] + 10 ; @logo_data[1] = @logos.size + 1
      end
  end
  
end

#==============================================================================
# ■ Scene Base
#==============================================================================
class Scene_Base
    
 #--------------------------------------------------------------------------
 # ● Terminate
 #--------------------------------------------------------------------------             
 alias mog_t_mlogos_main terminate
 def terminate        
     mog_t_mlogos_main 
     $logo_enabled = nil unless SceneManager.scene_is?(Scene_Title) or SceneManager.scene_is?(Scene_Load)
 end

end

#===============================================================================
# ■ SceneManager
#===============================================================================
class << SceneManager

  #--------------------------------------------------------------------------
  # ● First Scene Class
  #--------------------------------------------------------------------------  
  alias mog_t_mlogos_first_scene_class first_scene_class
  def first_scene_class
      $logo_enabled = nil
      mog_t_mlogos_first_scene_class
  end  
  
end

end
```
***
```Ruby
#==============================================================================
# +++ MOG - Clear Memory (v1.0) (Memory Leak Solution) +++
#==============================================================================
# By Moghunter
# https://atelierrgss.wordpress.com/
#==============================================================================
# O script limpa a memória utilizada pelo RpgMaker, basicamente dá um reset 
# no jogo sem que o jogador perca os dados dos jogo.
# Este script é voltado para quem estiver tendo problema com sobrecarregamento
# na memória Ram.
#==============================================================================
# UTILIZAÇÃO
#==============================================================================
# Basta usar o código abaixo através do comando chamar script.
#
# $game_system.clear_memory
#
#==============================================================================
# NOTA 
#==============================================================================
# Haverá uma pequena pausa no momento que a memória é limpa, portanto  é 
# aconselhavel limpar a memória durante uma cena ou quando for teleportar
# entre um mapa e outro.
#==============================================================================


#===============================================================================
# ■ SceneManager
#===============================================================================
class << SceneManager

  #--------------------------------------------------------------------------
  # ● First Scene Class
  #--------------------------------------------------------------------------  
  def first_scene_class
      $clear_memory ? Scene_Recover_Data : $BTEST ? Scene_Battle : Scene_Title
  end  
  
end

#===============================================================================
# ■ DataManager
#===============================================================================
class << DataManager
  
  #--------------------------------------------------------------------------
  # ● Save Game Temp
  #--------------------------------------------------------------------------
  def save_game_temp
      File.open("Quick_Save", "wb") do |file|
        $game_system.on_before_save
        Marshal.dump(make_save_header, file)
        Marshal.dump(make_save_contents, file)
      end
      return true
  end
  
  #--------------------------------------------------------------------------
  # ● Load Game Temp
  #--------------------------------------------------------------------------
  def load_game_temp
      File.open("Quick_Save", "rb") do |file|
        Marshal.load(file)
        extract_save_contents(Marshal.load(file))
        reload_map_if_updated
      end
      File.delete("Quick_Save") rescue nil
      return true
  end
  
end  

#===============================================================================
# ■ Game System
#===============================================================================
class Game_System
  
  #--------------------------------------------------------------------------
  # ● Clear_Memory
  #--------------------------------------------------------------------------        
  def clear_memory
      DataManager.save_game_temp
      reset = Win32API.new 'user32', 'keybd_event', %w(l l l l), ''
      reset.call(0x7B,0,0,0)
      sleep(0.1)
      $clear_memory = [RPG::BGM.last,RPG::BGS.last]
      reset.call(0x7B,0,2,0)
  end    
  
end  

#===============================================================================
# ■ Scene Recover Data
#===============================================================================
class Scene_Recover_Data
  
  #--------------------------------------------------------------------------
  # ● Main
  #--------------------------------------------------------------------------    
  def main
      SceneManager.clear
      DataManager.load_game_temp
      SceneManager.goto(Scene_Map)
      $clear_memory[0].replay rescue nil
      $clear_memory[1].replay rescue nil
      $clear_memory = nil
  end    
end

$mog_rgss3_clear_memory = true
```

## IMPORTANTE:

 [![NPM](https://img.shields.io/npm/l/react)](https://github.com/RonaldoBento/visual_novel/blob/main/LICENSE) 

Você tem todo o direito de usar esse material para seu próprio aprendizado. Espero que seja útil o conteúdo disponibilizado. Para rodar o jogo no seu computador (Windows) ou em outro dispositivo é preciso ter instalado o Programa RPG Maker VX Ace. Para jogar Game.exe apenas para Windows.