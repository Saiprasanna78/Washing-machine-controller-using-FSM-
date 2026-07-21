library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
entity washing_machine_fsm is
Port (
clk        : in  STD_LOGIC;
reset      : in  STD_LOGIC;
start      : in  STD_LOGIC;
water_full : in  STD_LOGIC;
wash_done  : in  STD_LOGIC;
rinse_done : in  STD_LOGIC;
spin_done  : in  STD_LOGIC;

water_valve : out STD_LOGIC;
motor_wash  : out STD_LOGIC;
motor_rinse : out STD_LOGIC;
motor_spin  : out STD_LOGIC;
buzzer      : out STD_LOGIC
);
end washing_machine_fsm;
architecture Behavioral of washing_machine_fsm is
type state_type is (IDLE, WATER_FILL, WASH, RINSE, SPIN, DONE);
signal current_state, next_state : state_type;
begin
-- State Register
process(clk, reset)
begin
if reset = '1' then
current_state <= IDLE;
elsif rising_edge(clk) then
current_state <= next_state;
end if;
end process;
-- Next State Logic
process(current_state, start, water_full, wash_done, rinse_done, spin_done)
begin
case current_state is
when IDLE =>
if start = '1' then
next_state <= WATER_FILL;
else
next_state <= IDLE;
end if;
when WATER_FILL =>
if water_full = '1' then
next_state <= WASH;
else
next_state <= WATER_FILL;
end if;
when WASH =>
if wash_done = '1' then
next_state <= RINSE;
else
next_state <= WASH;
end if;
when RINSE =>
if rinse_done = '1' then
next_state <= SPIN;
else
next_state <= RINSE;
end if;
when SPIN =>
if spin_done = '1' then
next_state <= DONE;
else
next_state <= SPIN;
end if;
when DONE =>
next_state <= IDLE;
when others =>
next_state <= IDLE;
end case;
end process;
-- Output Logic
process(current_state)
begin
-- Default outputs
water_valve <= '0';
motor_wash  <= '0';
motor_rinse <= '0';
motor_spin  <= '0';
buzzer      <= '0';
case current_state is
when IDLE =>
null;
when WATER_FILL =>
water_valve <= '1';
when WASH =>
motor_wash <= '1';
when RINSE =>
motor_rinse <= '1';
when SPIN =>
motor_spin <= '1';
when DONE =>
buzzer <= '1';
when others =>
null;
end case;
end process;
end Behavioral;# Washing-machine-controller-using-FSM-